---
title: Stories API Reference

language_tabs:
  - shell

toc_footers:
  #- <a href='#'>Sign Up for a Developer Key</a>
  #- <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Welcome to the Contently API. You can use our API to access your publication's taxonomy, stories, and to mark stories as published.

The current version of the API is v1. When backwards-incompatible changes are made to the API, we will bump the version number in the request path.

The current API endpoint is:

`https://api.contently.com/v1/`


# Authentication

> To authorize, specify the API key in the header:

```shell
curl --get https://api.contently.com/v1/stories \
   -H "Contently-Api-Key: <API_KEY>"
```

> Make sure to replace `<API_KEY>` with your API key.

You authenticate with the Contently API by specifying your publication's API key in the request header. The API key should be kept secret.

To change or reset your API key reach out to your account manager.

# Errors

> The body of the JSON response will contain an error message:

```shell
{
    "error": "Helpful error message"
}
```

When something goes wrong, we will respond with the appropriate HTTP status code.

| Error | What it means |
| ----- | ------------- |
| 200 OK | Everything worked as expected. |
| 400 Bad Request | Often missing a required parameter or updating a record with an invalid value. |
| 401 Unauthorized | No valid API key provided. |
| 403 Forbidden | Request understood but the action was invalid. |
| 404 Not Found | The requested item doesn't exist (It was deleted or never existed). |
| 500's | Something went wrong on our end. |

# Pagination

> 'Link' header format:

```shell
Link:
<https://api.contently.com/v1/stories?page=4&per_page=25>; rel="next",
<https://api.contently.com/v1/stories?page=2&per_page=25>; rel="prev",
<https://api.contently.com/v1/stories?page=1&per_page=25>; rel="first",
<https://api.contently.com/v1/stories?page=10&per_page=25>; rel="last"
```

> A request for a specific page of stories looks like:

```shell
curl --get https://api.contently.com/v1/stories \
       -H "Contently-Api-Key: <API_KEY>" \
       -d per_page=50 \
       -d page=2
```

Every response from an endpoint that returns a collection includes pagination information based on the request. This data can give you information about your previous request, but should **not** be used for traversing results (for traversing results see 'Link Header' below).

The following pagination information is included in the response header:

Header key | Description
--------- | ------- | -----------
X-Page | An Integer illustrating the current page number.
X-Per-Page | An Integer that specifies the page you want.
X-Offset | An Integer specifying the offset specified in the request.
X-Total | An Integer specifying the total number of records that matched your request.
Link | A string containing links to the next, previous, last and first pages.

#### Link header

We include a [link header](https://tools.ietf.org/html/rfc5988) that makes traversing pages easy by returning the next, previous, first and last pages. If there is no previous or next page, those links will be omitted. Similarly, if you are currently requesting the first or last pages, the respective links will also be omitted.

#### Making a request

You can specify the following parameters:

Parameter | Default | Description
--------- | ------- | -----------
per_page | 25 | A positive Integer that specifies the number of records per page, max is 100.
page | 1 | A positive Integer that specifies the page you want.
offset | 0 | A positive Integer that specifies the number of records after which you want your results to start.

# Stories

## Querying stories

```shell
# GET /stories

curl --get https://api.contently.com/v1/stories \
       -H "Contently-Api-Key: <API_KEY>"
 ```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "story_format": "Article / blog post",
    "title": "Example story",
    "published": true,
    "published_to_url": "http://example.com",
    "publication":{
      "id": 1,
      "name": "Example publication"
    },
    "creator":{
      "id": 1,
      "first_name": "Rando",
      "last_name": "Calrissian",
      "email": "rando@example.com"
    },
    "contributors":[
    {
      "id": 1,
      "first_name": "Rando",
      "last_name": "Calrissian",
      "email": "rando@example.com"
    }
    ],
    "custom_fields": [
      {
        "id": 1,
        "name": "Twitter",
        "content_type": "standard",
        "content": "<p>HTML content</p>",
        "file_name": "a_great_asset.png",
        "file_size_bytes": "67481",
        "mime_type": "image/png",
        "asset_url": "https://s3.amazonaws.com/a_great_asset.png",
        "publication_custom_field_id": 2
      }
    ],
    "due_at": 1421771582,
    "publish_at": 1421771582,
    "created_at": 1421771582,
    "published_at": 1421771582,
    "last_modified_at": 1421771582,
    "completed_at": 1421771582,
    "content": "<p>HTML body of story</p>",
    "status": "completed",
    "url": "https://contently.com/stories/1",
    "tags": [
      {
        "publication_tag_group_id": 1,
        "name": "Attribute name",
        "values": [
          { "id": 1, "name": "Attribute value 1" },
          { "id": 2, "name": "Attribute value 2" }
        ]
      }
    ],
    "seo_keywords": ["content marketing", "telling stories"],
    "assets": [
      {
        "id": 1,
        "url": "https://s3.amazonaws.com/another_great_asset.png",
        "name": "another_great_asset.png",
        "file_name": "another_great_asset.png",
        "file_size_bytes": "38182",
        "mime_type": "image/png",
        "created_at": 1421771582,
        "updated_at": 1421771582
      }
    ]
  }
]
```

> Specifying a sort:

```shell
curl --get https://api.contently.com/v1/stories \
       -H "Contently-Api-Key: <API_KEY>" \
       -d sort_by=last_modified_at \
       -d sort_direction=asc
```

> Specifying a date range:

```shell
curl --get https://api.contently.com/v1/stories \
  -H "Contently-Api-Key: <API_KEY>" \
  -d 'date_range[start]=1420732536' \
  -d 'date_range[end]=1420646154' \
  -d 'date_range[type]=completed_at'
```

There is a single endpoint available for querying stories.

`https://api.contently.com/v1/stories`

This endpoint returns an array of objects representing stories (details to the side).

Parameter | Required | Default | Description | Valid values
---- | ---- | ---- | -------- | ---------
published | No |  | A boolean value specifying a filter for published (true) or unpublished (false), respectively | true, false
sort_by | No | created_at | A String specifying the attribute of a story that you want to sort by. | See valid date fields below
sort_direction | No | desc | A String specifying whether you want the sorted stories to be returned in ascending or descending order. | asc, desc
date_range | No | | A JSON object representing the date range | See 'Specifying a date range below'

### Specifying a date range

You can query for certain dates ranges by including a JSON object containing the relevent data along with your request.

Only one **date_range** parameter can be specified per request.

If you choose to specify a date range, here are the required parameters:

Parameter | Required | Default | Description | Valid values
---- | ---- | ---- | -------- | ---------
start | Yes | | Unix timestamp | Any valid date
end | Yes | | Unix timestamp | Any valid date
type | Yes | | A String specifying the date attribute of a story that you want to search by, only one can be specified per request. | See valid date fields

### Valid date fields

<aside class="notice">All date / time fields are in [unix time](http://en.wikipedia.org/wiki/Unix_time)  - an Integer representing seconds since midnight Jan 1, 1970</aside>

Field name | Description
---- | ----
due_at | The date the first version of the story is due.
publish_at | The planned publish date for the story.
published_at | The date the story was actually published or marked published.
completed_at | The date the story was completed.
created_at | The date the story was created.
last_modified_at | The last time this story was edited (includes things like changing the title, but also actions like marking published).

## Story details

```shell
# GET /stories/:id

curl --get https://api.contently.com/v1/stories/:id \
   -H "Contently-Api-Key: <API_KEY>"
```

Returns fields for the specified story.

<aside class="notice">Note that the story must be completed, otherwise a 404 will be returned.</aside>

### Story JSON

```json
{
  "id": 1,
  "story_format": "Article / blog post",
  "title": "Example story",
  "published": true,
  "published_to_url": "http://example.com",
  "publication":{
    "id": 1,
    "name": "Example publication"
  },
  "creator":{
    "id": 1,
    "first_name": "Rando",
    "last_name": "Calrissian",
    "email": "rando@example.com"
  },
  "contributors":[
  {
    "id": 1,
    "first_name": "Rando",
    "last_name": "Calrissian",
    "email": "rando@example.com"
  }
  ],
  "custom_fields": [
    {
      "id": 1,
      "name": "Twitter",
      "content_type": "standard",
      "content": "<p>HTML content</p>",
      "file_name": "a_great_asset.png",
      "file_size_bytes": "67481",
      "mime_type": "image/png",
      "asset_url": "https://s3.amazonaws.com/a_great_asset.png",
      "publication_custom_field_id": 2
    }
  ],
  "due_at": 1421771582,
  "publish_at": 1421771582,
  "created_at": 1421771582,
  "published_at": 1421771582,
  "last_modified_at": 1421771582,
  "completed_at": 1421771582,
  "content": "<p>HTML body of story</p>",
  "status": "completed",
  "url": "https://contently.com/stories/1",
  "tags": [
    {
      "publication_tag_group_id": 1,
      "name": "Attribute name",
      "values": [
        { "id": 1, "name": "Attribute value 1" },
        { "id": 2, "name": "Attribute value 2" }
      ]
    }
  ],
  "seo_keywords": ["content marketing", "telling stories"],
  "assets": [
    {
      "id": 1,
      "url": "https://s3.amazonaws.com/another_great_asset.png",
      "name": "another_great_asset.png",
      "file_name": "another_great_asset.png",
      "file_size_bytes": "38182",
      "mime_type": "image/png",
      "created_at": 1421771582,
      "updated_at": 1421771582
    }
  ]
}
```

Field name | Type | Description
---- | ---- | ----
id | Integer | The unique identifier for the story.
status | String | The current status of the story.
story_format | String | The format of story, such as Article or Infographic.
title | String | The story title.
content | String (HTML) | The raw HTML of the story.
url | String | A link to the story on the Contently platform.
published | Boolean | Whether or not the story is flagged as published.
published_to_url | String | The URL where the story is published.
due_at | Unix datetime | The date the first version of the story was due.
publish_at | Unix datetime | The date the story is planned to be published.
published_at | Unix datetime | The date the story was actually published or marked published.
completed_at | Unix datetime | The date the story was completed.
created_at | Unix datetime | The date the story was created.
last_modified_at | Unix datetime | The last time this story was edited (includes things like changing the title, but also actions like marking published).
contributors | Array of Objs. | An array of the Contently users who worked on the story.
creator | Obj. | The user who created the story.
publication | Obj. | The publication includes the ID and name of the story's associated publication.
assets | Array of Objs. | An array of images embedded in the story content.
custom_fields | Array of Objs. | An array of the story's associated custom fields and their content (These are freeform and extensions of stories, things like excerpts, tweets, and header images). The publication_custom_field_id is an Integer that references the associated publication_custom_field configured at the publication level and available via the taxonomy endpoint.  </br></br> Possible values for content_type include: video, image, formatted_text, and standard. Video and image custom fields only accept a file upload. Standard custom fields are a legacy type and can include both text input and file uploads in a single custom field. The custom field's content_type is also available from the taxonomy endpoint.
story_fields | Array of Objs. | Deprecated, alias for custom_fields.
tags | Array of Objs. | Each tag group has a publication_tag_group_id (Integer, the unique ID of the tag group from the publication taxonomy), a name (String, also defined at the publication level), and an array of values. These are configured at a publication level and assigned to stories by users to categorize and describe them.
seo_keywords | Array of strings | An array of the story's SEO keywords.
story_attributes | Array of Objs. | Deprecated, alias for tags.
story_type | String | Deprecated, alias for story_format.

## Marking stories published

```shell
# PUT /stories/:id/mark_published

curl -X PUT https://api.contently.com/v1/stories/:id/mark_published \
       -H "Contently-Api-Key: <API_KEY>" \
       -H "Content-Length:0" \
       -d published_to_url=http://example.com \
       -d published_at=1420732536
```

> If you are using cURL, you must also specify the Content-Length in the header (any value will do).

**If you are integrating the Contently API with your CMS, it is vital that you notify the Contently platform that a story has been published. This allows us to display the published_to_url on the platform and provide analytics data for the story.**

This endpoint updates the specified story, flagging it as published and adding an item to the story's audit log (visible on the Contently platform) documenting the change. It enables analytics tracking and makes sure that published data on the platform is always up to date. It returns the story object if the update succeeded or an error message otherwise.

If a story is already published, you must include the **published_at** date in the request if you wish to override the existing date.

The **publish_at** date is the planned publish date that is specified in the Contently platform, this value is useful for automating publishing.

<aside class="notice">Note that attemping to mark published a story that is not currently completed will result in a 403 error.</aside>

Parameter | Required | Default | Description | Valid values
---- | ---- | ---- | -------- | ---------
published_to_url | No | | A String indicating the URL where the story was published | A valid URL beginning with 'http://' or 'https://'
published_at | No | The time the request is made (if the story has not already been published) | Unix timestamp indicating the time the story was pubilshed | Any valid date

# Webhook

```shell
# PUT /set_webhook

curl -X PUT https://api.contently.com/v1/set_webhook \
       -H "Contently-Api-Key: <API_KEY>" \
       -d webhook_url=http://example.com

```

> The response JSON is structured like this:

```json
{
  "webhook_url" : "http://example.com",
  "name" : "Publication name",
  "updated_at" : 1421771582
}
```

There is a single endpoint available for updating the webhook url for an integration.

`https://api.contently.com/v1/set_webhook`


The set webhook endpoint provides you with access to set an integration's webhook url.

To make publishing stories easier, we provide a webhook that allows you to push stories to your CMS after they are completed. When a user triggers the webhook on the Contently platform, it will submit a POST request to the URL you specify containing the JSON data for the story in the [format specified above](#story-details).

If security is a concern and you want to verify that Contently sent the webhook, you can simply use the story ID sent over (ignoring the rest of the request) to [request the story directly](#story-details) from our API. This will return the same information that we push to you via the webhook, but you will have initiated the request and specified the story based on the story ID initially pushed to you.

Parameter | Required | Default | Description | Valid values
---- | ---- | ---- | -------- | ---------
webhook_url | Yes | | A String indicating the URL where the stories will pushed once completed | A valid URL beginning with 'http://' or 'https://'

# Taxonomy

```shell
# GET /taxonomy

curl -G https://api.contently.com/v1/taxonomy \
       -H "Contently-Api-Key: <API_KEY>" \
```

> The response JSON is structured like this:

```json
{
    "integration": {
        "name": "Multi-Pub CMS Integration"
    },
    "publication_custom_fields": [
        {
            "id": 1,
            "name": "Twitter",
            "content_type": "standard"
        }
    ],
    "publication_tags": [
        {
            "id": 1,
            "name": "Attribute name",
            "values": [
                { "id": 1, "name": "Attribute value 1" },
                { "id": 2, "name": "Attribute value 2" }
            ]
        }
    ],
    "contributors":[
        {
            "id": 1,
            "first_name": "Rando",
            "last_name": "Calrissian",
            "email": "rando@example.com"
        }
    ],
    "story_formats": [
        "Article / blog post",
        "Case study",
        "eBook",
        "Email",
        "Facebook post",
        "Infographic",
        "LinkedIn post",
        "Original research",
        "Photo",
        "Podcast",
        "Presentation / brochure",
        "Tweet",
        "Video",
        "Whitepaper",
        "Other"
    ],
    "story_attributes": [
        {
            "name": "id",
            "type": "integer"
        },
        {
            "name": "title",
            "type": "string"
        },
        {
            "name": "published",
            "type": "boolean"
        },
        {
            "name": "published_to_url",
            "type": "string"
        },
        {
            "name": "publication",
            "type": "object"
        },
        {
            "name": "creator",
            "type": "object"
        },
        {
            "name": "due_at",
            "type": "datetime"
        },
        {
            "name": "publish_at",
            "type": "datetime"
        },
        {
            "name": "created_at",
            "type": "datetime"
        },
        {
            "name": "published_at",
            "type": "datetime"
        },
        {
            "name": "last_modified_at",
            "type": "datetime"
        },
        {
            "name": "completed_at",
            "type": "datetime"
        },
        {
            "name": "content",
            "type": "html"
        },
        {
            "name": "status",
            "type": "string"
        },
        {
            "name": "url",
            "type": "string"
        },
        {
            "name": "seo_keywords",
            "type": "array_of_strings"
        },
        {
            "name": "assets",
            "type": "array_of_objects"
        }
    ]
}
```

There is a single endpoint available for querying the combined taxonomy of all publications tied
to an integration.

`https://api.contently.com/v1/taxonomy`


The *taxonomy* endpoint provides you with access to publication level data that helps you understand your stories in a broader context. It returns integration, custom field, tag, contributor, story format, and story attribute data to enable mappings between data on the Contently platform and the respective fields on your side.

### Integration
Field name | Type | Description
---- | ---- | ----
name | String | Unique name of integration associated with given API key.

### Publication custom fields

Field name | Type | Description
---- | ---- | ----
id | Integer | The unique identifier for the custom field.
name | String | The name of the custom field.
content_type | String | The content type of the custom field. Possible values include: video, image, formatted_text, and standard. Video and image custom fields only accept a file upload. Standard custom fields are a legacy type and can include both text input and file uploads in a single custom field.

### Publication tags

Field name | Type | Description
---- | ---- | ----
id | Integer | The unique identifier for the tag
name | String | The name of the story attribute.
values | Array | An array of all the possible values that can be assigned to a story for a given story attribute.

### Contributors

Field name | Type | Description
---- | ---- | ----
id | Integer | The unique identifier for the user.
first_name | String | The user's first name.
last_name | String | The user's last name.
email | String | The user's unique email address.

### Story formats

An array of all the possible story formats that can be assigned to a story. This list is *not* customizable and is the same across all publications. Possible values are shown in the example response to the right.

### Story attributes

An array of story attribute key value pairs. Each entry contains a 'name' key, the value of which is the attribute name. These are the attributes available on the stories from the `/stories` endpoints. This array is *not* customizable and is the same across all publications. Possible values are shown in the example response to the right.

### Publication story fields

Deprecated, aliased to Publication custom fields

### Story types

Deprecated, aliased to Story formats

### Publication story attributes

Deprecated, aliased to Publication tags



