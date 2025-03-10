---
# Name of the event, <= 60 characters
title: Getting Started with Infrastructure as Code on AWS - Python
meta_desc: This introductory workshop is designed to help new users become familiar with the core concepts needed to effectively deploy resources on AWS using Pulumi.
meta_image:

# A featured webinar will display first in the list.
featured: false

# Webinars with unlisted as true will not be shown on the webinar list
unlisted: false

# Gated webinars will have a registration form and the user will need
# to fill out the form before viewing.
gated: true

# The layout of the landing page.
type: webinars

# External webinars will link to an external page instead of a webinar
# landing/registration page. If the webinar is external you will need
# set the 'block_external_search_index' flag to true so Google does not index
# the webinar page created.
external: false
block_external_search_index: false

# The url slug for the webinar landing page. If this is an external
# webinar, use the external URL as the value here.
url_slug: getting-started-with-iac-on-aws-python

# Content for the left hand side section of the page.
main:
    # Webinar title.
    title: Getting Started with Infrastructure as Code on AWS - Python

    event_type: workshop # workshop | event

    # URL for embedding a URL for ungated webinars.
    youtube_url:

    # Sortable date. The datetime Hugo will use to sort the webinars in date order.
    sortable_date: 2024-05-22T09:00:00.000-07:00

    # Duration of the webinar.
    duration: 90 minutes

    # "virtual" will be shown under "show virtual events only", otherwise shown as City, State (seattle, wa)
    location: virtual

    # Description of the webinar.
    description: |
        In this workshop, you will learn the fundamentals of Infrastructure as Code through a series of guided exercises using Pulumi’s Cloud Engineering platform. You will be introduced to Pulumi, an infrastructure as code platform, where you can use familiar programming languages to provision modern cloud infrastructure.

        This workshop is designed to help users completely new to Pulumi to become familiar with the core concepts to be effective with the Pulumi Infrastructure as Code platform. We will guide you through the Pulumi platform with diagrams and a series of hands on exercises to help you understand the building blocks available in Pulumi."

    learn:
        - How to use Python with Pulumi.
        - The basics of the Pulumi Programming Model
        - How to provision, update, and destroy AWS resources

    # The webinar presenters
    presenters:
        - name: Marina Novikova
          role: Sr. Partner Solutions Architect, AWS
          photo: /images/team/marina-novikova.jpg

    # case-sensitive
    tags:
        level: Beginner # Beginner, Intermediate, Advanced
        topics: []
        languages: ["Python"]
        clouds: ["AWS"]

# The right hand side form section.
form:
    # HubSpot form id.
    hubspot_form_id: 5d97ebfb-ea71-4887-b6ae-f74347948e38
    salesforce_campaign_id: 701PQ000009K1SbYAK
---