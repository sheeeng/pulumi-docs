---
# Name of the event, <= 60 characters
title: KubeCon North America
meta_desc: Ready to streamline your Kubernetes setup? Visit Pulumi in booth R1 to hear about universal infrastructure as code for Kubernetes.
meta_image:

# A featured webinar will display first in the list.
featured: false

# Webinars with unlisted as true will not be shown on the webinar list
unlisted: false

# Gated webinars will have a registration form and the user will need
# to fill out the form before viewing.
gated: false

# The layout of the landing page.
type: webinars

# External webinars will link to an external page instead of a webinar
# landing/registration page. If the webinar is external you will need
# set the 'block_external_search_index' flag to true so Google does not index
# the webinar page created.
external: true
block_external_search_index: true

# The url slug for the webinar landing page. If this is an external
# webinar, use the external URL as the value here.
url_slug: /kubecon

# Content for the left hand side section of the page.
main:
    # Webinar title.
    title: KubeCon North America

    event_type: event # workshop | event

    # URL for embedding a URL for ungated webinars.
    youtube_url:

    # Sortable date. The datetime Hugo will use to sort the webinars in date order.
    sortable_date: 2024-11-12T09:00:00-07:00

    # Duration of the webinar.
    duration: 1 hour

    # "virtual" will be shown under "show virtual events only", otherwise shown as City, State (seattle, wa)
    location: Salt Lake City, UT

    # Description of the webinar.
    description:

    # The webinar presenters
    presenters:

    # case-sensitive
    tags:
        level: # Beginner, Intermediate, Advanced
        topics: ["Kubernetes"]
        languages: []

# The right hand side form section.
form:
    # HubSpot form id.
    hubspot_form_id:
    salesforce_campaign_id:
---