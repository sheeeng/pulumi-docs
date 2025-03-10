---
title_tag: Building a Component Resource | Pulumi Tutorials
title: Building a Component Resource
layout: topic
description: Try creating a component resource, or a logical grouping of code that Pulumi recognizes as a resource.
meta_desc: Learn how to build a component resource, or a logical grouping of code that Pulumi recognizes as a resource, in this tutorial.
meta_image: meta.png
weight: 3
estimated_time: 10
aliases:
    - /learn/abstraction-encapsulation/component-resources/
---

Now that we have a better understanding of why abstraction and encapsulation are valuable concepts for Pulumi programs, let's explore what happens when we start working in larger teams at scale.

A resource in Pulumi terms is basically a building block. That building block could be a base component like an `s3.Bucket` object that's managed by external providers like your favorite cloud provider. Alternatively, a resource could be a group of other resources that implements abstraction and encapsulation for reasons like defining a common pattern. This latter type of resource is called a [_Component Resource_](/docs/concepts/resources#components) in Pulumi's terminology.

## Deciding to create a component resource

Why would you make a component resource instead of just a "simple" logical grouping that you can call and use like a generic library full of classes and functions? A component resource shows up in the Pulumi ecosystem just like any other resource. That means it has a trackable state, appears in diffs, and has a name field you can reference that refers to the entire grouping.

We've actually already started creating a component resource in the encapsulation part when we made a new class that built up an `s3.Bucket` and an `s3.BucketPolicy`. Let's now turn that logical grouping into an actual component resource.

## Converting to a component resource

When we're converting to a component resource, we subclass `pulumi.ComponentResource` so that our new component resource can get all of the lovely benefits of a resource (state tracking, diffs, name fields, etc.) that other resources have.

{{% choosable language typescript %}}

In TypeScript, we do this by extending `pulumi.ComponentResource` and calling `super()` in the class constructor. This call ensures that Pulumi registers the component resource as a resource properly.

{{% /choosable %}}

{{% choosable language python %}}

In Python, we do this by passing `pulumi.ComponentResource` in the class definition and calling `super()` in the initializer. This call ensures that Pulumi registers the component resource as a resource properly.

{{% /choosable %}}

{{< chooser language "typescript,python" />}}

{{% choosable language typescript %}}

```typescript
import * as pulumi from "@pulumi/pulumi";
import * as aws from "@pulumi/aws";

type PolicyType = "default" | "locked" | "permissive";

// Create a class that encapsulates the functionality by subclassing
// pulumi.ComponentResource.
class OurBucketComponent extends pulumi.ComponentResource {
    public bucket: aws.s3.BucketV2;
    private bucketPolicy: aws.s3.BucketPolicy;

    private policies: { [K in PolicyType]: aws.iam.PolicyStatement } = {
        default: {
            Effect: "Allow",
            Principal: "*",
            Action: [
                "s3:GetObject"
            ],
        },
        locked: {
            Effect: "Allow",
            /* ... */
        },
        permissive: {
            Effect: "Allow",
            /* ... */
        },
    };

    private getBucketPolicy(policyType: PolicyType): aws.iam.PolicyDocument {
        return {
            Version: "2012-10-17",
            Statement: [{
                ...this.policies[policyType],
                Resource: [
                    pulumi.interpolate`${this.bucket.arn}/*`,
                ],
            }],
        }
    };

    constructor(name: string, args: { policyType: PolicyType }, opts?: pulumi.ComponentResourceOptions) {

        // By calling super(), we ensure any instantiation of this class
        // inherits from the ComponentResource class so we don't have to
        // declare all the same things all over again.
        super("pkg:index:OurBucketComponent", name, args, opts);

        this.bucket = new aws.s3.BucketV2(name, {}, { parent: this });

        this.bucketPolicy = new aws.s3.BucketPolicy(`${name}-policy`, {
            bucket: this.bucket.id,
            policy: this.getBucketPolicy(args.policyType),
        }, { parent: this });

        // We also need to register all the expected outputs for this
        // component resource that will get returned by default.
        this.registerOutputs({
            bucketName: this.bucket.id,
        });
    }
}

const bucket = new OurBucketComponent("laura-bucket-1", {
    policyType: "permissive",
});

export const bucketName = bucket.bucket.id;
```

{{% /choosable %}}

{{% choosable language python %}}

```python
import pulumi
import pulumi_aws as aws
import pulumi_aws_native as aws_native

# Create a class that encapsulates the functionality while subclassing the ComponentResource class (using the ComponentResource class as a template).
class OurBucketComponent(pulumi.ComponentResource):
    def __init__(self, name_me, policy_name='default', opts=None):
        # By calling super(), we ensure any instantiation of this class inherits from the ComponentResource class so we don't have to declare all the same things all over again.
        super().__init__('pkg:index:OurBucketComponent', name_me, None, opts)
        # This definition ensures the new component resource acts like anything else in the Pulumi ecosystem when being called in code.
        child_opts = pulumi.ResourceOptions(parent=self)
        self.name_me = name_me
        self.policy_name = policy_name
        self.bucket = aws_native.s3.Bucket(f"{self.name_me}")
        self.policy_list = {
            'default': {
                'Effect': 'Allow',
                'Principal': "*",
                'Action': [
                    "s3:GetObject"
                ],
            },
            'locked': {
                'Effect': 'Allow',
                # ...
            },
            'permissive': {
                'Effect': 'Allow',
                # ...
            },
        }
        # We also need to register all the expected outputs for this component resource that will get returned by default.
        self.register_outputs({
            "bucket_name": self.bucket.bucket_name
        })

    def define_policy(self):
        policy_name = self.policy_name
        try:
            json_data = self.policy_list[f"{policy_name}"]
            policy = self.bucket.arn.apply(lambda arn: json.dumps(json_data).replace('fakeobjectresourcething', arn))
            return policy
        except KeyError as err:
            add_note = "Policy name needs to be 'default', 'locked', or 'permissive'"
            print(f"Error: {add_note}. You used {policy_name}.")
            raise

    def set_policy(self):
        bucket_policy = aws.s3.BucketPolicy(
            f"{self.name_me}-policy",
            bucket=self.bucket.id,
            policy=self.define_policy(),
            opts=pulumi.ResourceOptions(parent=self.bucket)
        )
        return bucket_policy


bucket1 = OurBucketComponent('laura-bucket-1', 'default')
bucket1.set_policy()

pulumi.export("bucket_name", bucket1.bucket.id)
```

{{% /choosable %}}

With the call to `super()`, we pass in a name for the resource, which [we recommend](/docs/concepts/resources/components#authoring-a-new-component-resource) being of the form `<package>:<module>:<type>` to avoid type conflicts since it's being registered alongside other resources like the Bucket resource we're calling (`aws:s3:BucketV2`).

{{% choosable language python %}}

That last call in the init, `self.register_outputs({})`, passes Pulumi the expected outputs so Pulumi can read the results of the creation or update of a component resource just like any other resource, so don't forget that call! You can [register default outputs using this call](/docs/concepts/resources/components#registering-component-outputs), as well. It's not hard to imagine we will always want the bucket name for our use case, so we pass that in as an always-given output for our component resource.

{{% /choosable %}}

{{% choosable language typescript %}}

That last call in the init, `this.registerOutputs({})`, passes Pulumi the expected outputs so Pulumi can read the results of the creation or update of a component resource just like any other resource, so don't forget that call! You can [register default outputs using this call](/docs/concepts/resources/components#registering-component-outputs), as well. It's not hard to imagine we will always want the bucket name for our use case, so we pass that in as an always-given output for our component resource.

{{% /choosable %}}

From here, you can deploy it and get your custom resource appearing in the resource tree in your terminal! You can also share it with others so they can import the resource and use it without ever needing to understand all of the underlying needs of a standard storage system on AWS.

---

Congratulations! You've now finished this tutorial on abstraction and encapsulation in Pulumi programs! In this tutorial, you've learned about thinking of code in abstract forms, wrapping up logical groupings of code to make reuse easier, and building with component resources to make those logical groupings something that Pulumi recognizes.

There's a lot more to explore regarding this topic in Pulumi. We're working on more tutorials, but for now, check out some more resources:

* [An AWS/Python example](https://github.com/pulumi/examples/tree/master/aws-py-wordpress-fargate-rds)
* [An Azure/Python example](https://github.com/pulumi/examples/tree/master/classic-azure-py-webserver-component)
* [A Google Cloud/Python example](https://github.com/pulumi/examples/tree/master/gcp-py-network-component)

Go build new things, and watch this space for more learning experiences with Pulumi!

{{< tutorials/stepper >}}
