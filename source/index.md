---
title: Industry2go API 2.0 Reference

includes:
  - errors
  - queries

search: true
---

# Introduction

Industry2go Developers Guide API 2.0

All the technical information and the concepts behind the data involved and other necessary information about Industry2go's back-end API is contained here.

# Understanding the concept of the API

In our backend we make use of the concepts of taxonomies and terms. These concepts, which will be detailed next, makes it possible to create multiple types of relationships among pieces of data and simplify the handle of common data.

## The Term concept

Term is a generic entity that encapsulates data that's common to all the other entities. A term is like an abstract root class, this means that you never create a term by itself, but all the concrete entities (sections, products, documents, etc.) are backed by a term. However, the term is the entity that is in fact used to relate with others terms.

All entities have an `id` (a normal identifier field, which is unique in the context) and have a `term_id` (a globally unique `id`, for the term information of that entity). For instance, if we take an empty database and create a Document, this new Document will have an `id` equal to 1 and a `term_id` also equal to 1. Now if we create a Product, it'll have an `id` equal to 1 but a `term_id` equal to 2 (remember, the `term_id` is unique!). Now if we create a new Document, it'll have an `id` equal to 2 and a `term_id` equal to 3, and so on.

When it comes to creating relationships, we always use the `term_id` of the involved entities, e.g., if you want to link a Product Brand to a parent Section we set the `parent_term_id` of the Product Brand to the `term_id` from the Section.

All entities that are involved in multiple relationships have an array in the response body named `relations`. This array carries the term information of all related terms.

## The Taxonomy concept

All entities are terms, but the developer need know which is the concrete entity for a term, i.e., if a term is a Document or a Product, for instance. This is resolved by Taxonomy Concept, where each and every term belongs to a taxonomy.
  
Take the following dictionary as an example. `Section` is a taxonomy and its `taxomomy_id` is 1 (this is not the same of an "instance" of a Section with `id` equal to 1). The developer can always rely on the `taxonomy_id` of a term to know which is the "concrete" entity of that term.

> Example of generated data after a CREATE action:

```json
{
   "metadata": {
       "url": "http://"
   },
   "entity": {
       "section": {
           "id": 33,
           "name": "Section test EDITED",
           "term_id": 4562,
           "tag": "INNodeAdditionalServices"
       },
       "term": {
           "id": 4562,
           "name": "Section test EDITED",
           "taxonomy_id": 1,
           "weight": "0",
           "on_demand_enabled": true,
           "on_demand_depth": 0,
           "deleted": false,
           "created": "2015-06-08T04:16:17+0200",
           "modified": "2015-06-08T04:27:30+0200"
       }
   }
}

```

<aside class="success">
Check the example in the right side, is a json response of a created Section
</aside>
After the developer has created a new Section, we can see the data created in the response body, and in the term dictionary you can see the `taxonomy_id` equal to 1, indicating that this term comes from a Section.

Developers can access the list of taxonomies by making a request via the API. More instructions are coming soon to this document.

# Understanding how the API works

The first thing a developer should know is that s/he can, optionally, use the back-end to calculate the content that needs to be download on the client side. In order to do this, each and every download of content has to be confirmed (there's a call in the API just for that). If the client programmer handles synchronization on the client side or is always interested in the whole content, s/he should use the API in passing a parameter to explicitly bypass the updates engine. The default behavior is calculating the updates, but this should change in the future.

All of the API calls follow the RESTful principle, making extensive use of HTTP verbs/codes and error messages.

## Query Paramaters

<aside class="notice">Query parameters are passed via the request URL.</aside>

List of accepeted parameters:


Parameter | Meaning
---------- | -------
token | This parameter is always required in a request, and it represents the registration token of the device which is making the request.
updateEnabled | This parameter toggles On and Off the update calculation on the request processing; the default value for this parameter is `true`, in case this parameter is not present. 
limit | The limit parameter is used in GET requests, to limit you body content, this parameter is very important, a long body request can turn the respose slow.
page | Who's making the request can walk between pages of content using  ?page=number query parameter.


#The API for CRUD operations

To start using the api you will need a valid device token.
The api will use a url like this `http://industry2go.example.com/api/` always with `/api/`, following the restful principles, the parameters that will be attached after this `api/` is the resource that the developer will handle, like documents, or products.

##Registering a new Device

Always that we will create a new record in the API we need send a request with type `POST`.
  To register a new divice the developer need add after the `api/` route the name of the `resource`, for device the name is devices. And set the type of request `method` to `POST`.
In the body of the request will be needed one parameter named token that will be the name/code of the `token`.

> Response of created device:

```json
{
    "metadata": {
        "success": true,
        "count": 1,
        "total_remaining": 0,
        "has_more": false
    },
    "results": {
        "devices": {
            "id": 2074,
            "token": "test_token",
            "created": "2015-06-08T06:00:52+0200",
            "updated": "2015-06-08T06:00:52+0200"
        }
    }
}

```

### HTTP Request


`POST http://industry2go.example.com/api/devices/`

### Request body parameters

Parameter | Nullable | Description
--------- | ------- | -----------
token | false | your token name/code to grant api access.


<aside class="success">
  The response will return a status code 201(created) with the generated token informations.
</aside>


##GET from market versions

With a registered `token` the developer can make requests, the first request that we will see is the market version list.


### HTTP Request

`GET http://industry2go.example.com/api/market_versions/?token=test_token&updateEnabled=false`

> Response: List of market versions:

```json
{
    "metadata": {
        "success": true,
        "count": 3,
        "total_remaining": 0,
        "has_more": false
    },
    "results": [
        {
            "market_version": {
                "id": 1,
                "name": "Deutsche Sprachversion",
                "term_id": 1150,
                "alias": "DE",
                "language_id": "de",
                "enabled": 1
            },
            "term": {
                "id": 1150,
                "name": "Deutsche Sprachversion",
                "taxonomy_id": 7,
                "weight": 2,
                "on_demand_enabled": false,
                "on_demand_depth": 0,
                "deleted": false,
                "created": "2014-07-30T13:19:06+0200",
                "modified": "2015-03-10T13:36:44+0100"
            },
            "needed_update": {
                "id": 51159,
                "term_id": 1150,
                "version_id": 12546,
                "device_id": 2074,
                "kind": "create"
            }
        },
        {
            "market_version": {
                "id": 2,
                "name": "English Language Version",
                "term_id": 1151,
                "alias": "EN",
                "language_id": "en",
                "enabled": 1
            },
            "term": {
                "id": 1151,
                "name": "English Language Version",
                "taxonomy_id": 7,
                "weight": 1,
                "on_demand_enabled": false,
                "on_demand_depth": 0,
                "deleted": false,
                "created": "2014-07-30T13:19:13+0200",
                "modified": "2015-03-10T13:36:38+0100"
            },
            "needed_update": {
                "id": 51160,
                "term_id": 1151,
                "version_id": 12545,
                "device_id": 2074,
                "kind": "create"
            }
        },
        {
            "market_version": {
                "id": 5,
                "name": "French Language Version",
                "term_id": 2957,
                "alias": "FR",
                "language_id": "fr",
                "enabled": 1
            },
            "term": {
                "id": 2957,
                "name": "French Language Version",
                "taxonomy_id": 7,
                "weight": 3,
                "on_demand_enabled": false,
                "on_demand_depth": 0,
                "deleted": false,
                "created": "2015-03-06T14:11:43+0100",
                "modified": "2015-03-07T16:41:30+0100"
            },
            "needed_update": {
                "id": 51161,
                "term_id": 2957,
                "version_id": 11358,
                "device_id": 2074,
                "kind": "create"
            }
        }
    ]
}
```
<aside class="success">
  The response will return a status code 200(status code) with the count of records.
</aside>
The developer will use the market version term id to create relations between content and market versions, all entities are related by term.



##GET list of a specific Entity

The developer will use the same of list market versions to list another entities like this:


### HTTP Request

`GET http://industry2go.example.com/api/sections/?token=test_token&updateEnabled=false`

>Response: list of sections(or the called entity)

```json
{
    "metadata": {
        "success": true,
        "count": 1,
        "total_remaining": 31,
        "has_more": true
    },
    "results": [
        {
            "section": {
                "id": 1,
                "name": "Siemens Industry",
                "term_id": 1,
                "tag": "INNodeCompanyWithChildren",
                "size": "3803049",
                "md5": "95295dee75bb7f7278fd99596083ca2d"
            },
            "term": {
                "id": 1,
                "name": "Siemens Industry",
                "taxonomy_id": 1,
                "weight": 60,
                "on_demand_enabled": true,
                "on_demand_depth": 1,
                "deleted": false,
                "created": "2014-03-21T15:03:12+0100",
                "modified": "2015-02-09T15:25:53+0100"
            }
        }
    ]
}
```

##POST Creating a new entity

To create a new entity the developer will need just add the `resource` in the `url`, and the `method POST`, in the body of the request that have the entity data.


<aside class="notice">
  The paremeter `updateEnabled=false` is needed only in GET requests
</aside>

###Creating a new Section

### HTTP Request

`POST http://industry2go.example.com/api/sections/?token=test_token`

#### Request body parameters

Parameter | Nullable | Description
--------- | ------- | -----------
name | false | The name of the section can be string max 255 characters
tag | true | Tags are used only to show in the correct area on mobile side
background | true | Input file (only images)
icon | true | Input file (only images)
marketVersions | false | market version term id
term[weight]   | false | weight of the entity to show in mobile side


>Response: created entity data (status code 201(created))

```json
{
    "metadata": {
        "url": "http://"
    },
    "entity": {
        "section": {
            "id": 34,
            "name": "test name",
            "tag": "test tag"
        },
        "term": {
            "id": 4564,
            "name": "test name",
            "weight": "0",
            "on_demand_enabled": "0",
            "on_demand_depth": "0",
            "deleted": "0",
            "created": "2015-06-11T04:20:21+0200",
            "modified": "2015-06-11T04:20:21+0200"
        }
    }
}
```

###Creating a new Product Brand

### HTTP Request

`POST http://industry2go.example.com/api/product_brands/?token=test_token`

#### Request body parameters

Parameter | Nullable | Description
--------- | ------- | -----------
name | false | The name of the product brands can be string max 255 characters
info | true | Text area to add a long text about the product brand
background | true | Input file (only images)
icon | true | Input file (only images)
marketVersions | false | market version term id
term[weight]   | false | weight of the entity to show in mobile side
parentTermId   | false | section term_id that will be parent of this entity


###Creating a new Product Family

### HTTP Request

`POST http://industry2go.example.com/api/product_families/?token=test_token`

#### Request body parameters

Parameter | Nullable | Description
--------- | ------- | -----------
name | false | The name of the section can be string max 255 characters
info | true | Text area to add a long text about the product family
background | true | Input file (only images)
marketVersions | false | market version term id
term[weight]   | false | weight of the entity to show in mobile side
parentTermId   | false | section term_id that will be parent of this entity


###Creating a new Product

### HTTP Request

`POST http://industry2go.example.com/api/products/?token=test_token`

#### Request body parameters

Parameter | Nullable | Description
--------- | ------- | -----------
name | false | The name of the section can be string max 255 characters
info | true | Text area to add a long text about the product family
background | true | Input file (only images)
marketVersions | false | market version term id
term[weight]   | false | weight of the entity to show in mobile side
parentTermId   | false | section term_id that will be parent of this entity


###Creating a new Document

### HTTP Request

`POST http://industry2go.example.com/api/documents/?token=test_token`

#### Request body parameters

Parameter | Nullable | Description
--------- | ------- | -----------
name | false | The name of the document can be string max 255 characters
thumblabel | false | The thumblabel of the document can be string max 60 characters
approvalNumber | false | a numeric input [0~9]
marketVersions | false | market version term id
term[weight]   | false | weight of the entity to show in mobile side
termsTerms   | false | section term_id that will be parent of this entity
pdfStartPage | false | a numeric input [0 ~ 9]
file | false | file input, that accepts PDF, Presentations and Videos, the kind of file will be auto detected after submit


##PUT Updating entities

To update a entity, basically the developer need add 3 fields in the request body and one more in the url. 
Using our example domain, here we have `sections/term_id`, the method to make an update is `PUT` but if the language, or browser dont support `PUT` method can be using a `POST` with a parameter named `_method = PUT` in the body of the request.

### HTTP Request

`POST http://industry2go.example.com/api/sections/1?token=test_token`

#### Request body parameters

Parameter | Nullable | Description
--------- | ------- | -----------
name | false | The name of the section can be string max 255 characters
tag | true | Tags are used only to show in the correct area on mobile side
background | true | Input file (only images)
icon | true | Input file (only images)
marketVersions | false | market version term id
term[weight]   | false | weight of the entity to show in mobile side
id | false | numeric [0~9]
termId | false |  numeric [0~9]
_method | true | if the request dont support `PUT`, use a `POST` with this field with value `PUT`
