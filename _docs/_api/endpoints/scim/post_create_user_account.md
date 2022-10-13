---
nav_title: "POST: Create New Dashboard User Account"
article_title: "POST: Create New Dashboard User Account"
alias: /post_create_user_account/
search_tag: Endpoint
page_order: 4
layout: api_page
page_type: reference
description: "This article outlines details about the Create New Dashboard User Account Endpoint."

---

{% api %}
# Create a new dashboard user account
{% apimethod post %}
/scim/v2/users
{% endapimethod %}

This endpoint allows you to create a new dashboard user account by specifying email, given and family names, permissions (for setting permissions at the company, app group, and team level). For information on how to obtain a SCIM token, visit [Automated user provisioning]({{site.baseurl}}/scim/automated_user_provisioning/). 

## Rate limit

{% multi_lang_include rate_limits.md endpoint='create dashboard user' %}

## Request body
```
Content-Type: application/json
X-Request-Origin: YOUR-REQUEST-ORIGIN-HERE
Authorization: Bearer YOUR-SCIM-TOKEN-HERE
```
```
{
  "schemas": (required, array of strings),
  "userName": (required, string),
  "name": (required, JSON object),
  "department": (required, string),
  "permissions": (required, JSON object)
}
```

## Request parameters

| Parameter | Required | Data type | Description |
| --------- | -------- | --------- | ----------- |
| Schemas | Required | Array of strings | Expected SCIM 2.0 schema name for user object. |
| `userName` | Required | String | The user’s email address |
| `name` | Required | JSON object | This object contains the user's first name and last name |
| `department` | Required | String | Valid department string from the [department string table]({{site.baseurl}}/scim_api_appendix/#department-strings). |
| `permissions` | Required | JSON object | Permissions object as described in the [Permissions object]({{site.baseurl}}/scim_api_appendix/#permissions-object) section. |
{: .reset-td-br-1 .reset-td-br-2 .reset-td-br-3  .reset-td-br-4}

## Example request
```json
curl --location --request POST 'https://rest.iad-01.braze.com/scim/v2/Users' \
--header 'Content-Type: application/json' \
--header 'X-Request-Origin: YOUR-REQUEST-ORIGIN-HERE' \
--header 'Authorization: Bearer YOUR-SCIM-TOKEN-HERE' \
--data raw '{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "user@test.com",
    "userName": "user@test.com",
    "name": {
        "givenName": "Test",
        "familyName": "User"
    },
    "department": "finance",
    "permissions": {
        "companyPermissions": ["manage_company_settings"],
        "appGroup": [
            {
                "appGroupName": "Test App Group",
                "appGroupPermissions": ["basic_access","send_campaigns_canvases"],
                "team": [
                    {
                         "teamName": "Test Team",                  
                         "teamPermissions": ["basic_access","export_user_data"]
                    }
                ]
            } 
        ]
    }
}' 
```

## Response
```json
CContent-Type: application/json
X-Request-Origin: YOUR-REQUEST-ORIGIN-HERE
Authorization: Bearer YOUR-SCIM-TOKEN-HERE
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "user@test.com",
    "userName": "user@test.com",
    "name": {
        "givenName": "Test",
        "familyName": "User"
    },
    "department": "finance",
    "lastSignInAt": "Thursday, January 1, 1970 12:00:00 AM",
    "permissions": {
        "companyPermissions": ["manage_company_settings"],
        "appGroup": [
            {
                "appGroupId": "241adcd25789fabcded",
                "appGroupName": "Test App Group",
                "appGroupPermissions": ["basic_access","send_campaigns_canvases"],
                "team": [
                    {
                         "teamId": "2519dafcdba238ae7",
                         "teamName": "Test Team",                  
                         "teamPermissions": ["basic_access","export_user_data"]
                    }
                ]
            } 
        ]
    }
}
```

### Error states

If a user with this email address already exists in Braze, the endpoint will respond with:

```json
HTTP/1.1 409 Conflict
Date: Tue, 10 Sep 2019 02:22:30 GMT
Content-Type: text/json;charset=UTF-8

{
  "schemas": ["urn:ietf:params:scim:api:messages:2.0:Error"],
  "detail": "User already exists in the database.",
  "status": 409
}
```

{% endapi %}


