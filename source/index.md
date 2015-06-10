---
title: API Reference


toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors
  - queries

search: true
---

# Introduction

Industry2go Developers Guide API 2.0

All needed informations about our API is wrote here, the technical information and the concept about how it works.



# Understanding the concept of the API

The backend there the concept of taxonomy and terms, using this way is possible and easy to create multiple types of relationships andeasy to handle the data.

##The Term concept
  Terms are all of our entities inside of our project eg: sections, products, documents. All of them are belongs to a Term, a term is the real “entity” that will relate with others terms.
All entities have an id(normal identifier field) and have a term_id(that means your term_id related with term).

To create relations, all that you will need is know the term_id of the needed entity eg: a product brand have a term_id 100 to create a relation between a product family and this product brand you just need set the parent_term_id of the product family.

All entities with multiple relations have a field in the request body named relations, that field have all of the relation terms, this turn easy to walk between this relations.

##The Taxonomy concept
All entities are terms, but the developer need know what this terms are, to solve this needs we have the Taxonomy Concept, all terms belongs to a taxonomy, a taxonomy is the kind of entity they are, eg: a document with id 1 and term id 100, the term have a field named taxonomy id that refers to taxonomy Document.
  
The developer always can use the Taxonomy id to know what kind of entity the term are.

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
The developer have created a new section, we can see the body informations and in the term you can see the relation with the Taxonomy, the taxonomy id 1 is Section.

  Developers can access the list of taxonomies making a request in the api, that will be shown soon in this document.




# Understanding how the API works

The first stuff that a developer who will use this api need know is the difference between update API and normal APi. The update api is used for devices, that can download content and get updates, developers should use the normal api that will show all content without update rules.
  All of the requestsfollow the RESTful principle, using correctly http codes, error messages and more.

## Query Paramaters

<aside class="notice">Query parameters are the way to send parameters attached in the api URL, this way is used frequently in this API..</aside>

List of accepeted parameters::


Parameter | Meaning
---------- | -------
token | The most important query parameter, that will be always needed in a request, this is the token of who are making the request.
updateEnabled | This is the way to switch between normal and update api, developer should always send ?updatedEnabled=false to use the normal api to make requests type GET
limit | The limit parameter is used in GET requests, to limit you body content, this parameter is very important, a long body request turn the respose slow
page | Who are making the request can walk between pages of content using  ?page=number query parameter.



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