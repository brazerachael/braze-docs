---
nav_title: Talon.One
alias: /partners/talonone/
description: "This article outlines the partnership between Braze and Talon.One, a promotion engine that allows you to launch contextual 1-to-1 coupon, referral, discount, and loyalty campaigns quickly and efficiently."
---

# Talon.One

[Talon.One](https://talon.one/) provides personalized incentives for your mobile marketing CRM and gives you the ability to launch contextual 1-to-1 coupon, referral, discount, and loyalty campaigns quickly and efficiently.

Coupled with the use of a flexible rules engine to boost your conversion and retention rates without burning the promotion budget, Talon.One and Braze's partnership can help take your loyalty or coupon program to the next level by sending codes generated by Talon.One to your audience through Braze.

{% alert important %}
This partnership is in early access beta. All features may not perform as exactly described. Please reach out to your Braze Account Manager for more information.
{% endalert %}

## Pre-Requisites

| Requirement | Origin | Description |
| --------- | ------------ | ---------- |
| Braze Account | [Braze Settings](https://dashboard.braze.com/sign_in) | You will need an account with Braze, as well as an active Braze campaign that vouchers and codes will be generated for. |
{: .reset-td-br-1 .reset-td-br-2 .reset-td-br-3}

{% alert warning %}
Talon.One **_requires_** a rate limit of 500 messages per minute.

You can modify your rate limit using Braze. Find out how [here]({{site.baseurl}}/user_guide/engagement_tools/campaigns/testing_and_more/rate-limiting/#delivery-speed-rate-limiting).
{% endalert %}

## API Integration

### Set Up Coupons in Talon.One

You can modify the form of generated codes for any campaign by navigating to the Coupon Code Generator for that campaign found within the Talon.One dashboard under **Campaign > Settings > Coupon Code Generator**.

![Talon.One Coupon Settings]({% image_buster /assets/img/talonone_coupon_settings.png %})

### Endpoint Usage

{% alert note %}
Note that as of December 2019, custom endpoints are no longer given out, if you have a pre-existing custom endpoint, you may continue to use it. For a list of our available endpoints, <a href="{{site.baseurl}}/api/basics/#endpoints">click here</a>.
{% endalert %}

Because Braze's Connected Content only supports **string** data types, a custom endpoint must be used to convert everything to the correct data type.
This endpoint contains the following built-in properties:

- `applicationID` (required)
- `campaignID` (required)
- `identifier` (required)
- `integrationID`
- `startDate`
- `expiryDate`

#### Endpoint Use Examples
{% tabs %}
{% tab Only Required Properties %}
##### Example One: Only Required Properties

```bash
curl https://demo.talon.one/v2/integration/braze/createcoupon \
 -X POST \
 -H 'Authorization: ApiKey-v1 [YOUR_API_KEY]' \
 -d '{
        "applicationID": "1",
        "campaignID: "1",
        "identifier": "an-example-identifier"
}'
```
{% endtab %}
{% tab All Built-In Properties %}
##### Example Two: All Built-In Properties

```bash
curl https://demo.talon.one/v2/integration/braze/createcoupon \
 -X POST \
 -H 'Authorization: ApiKey-v1 [YOUR_API_KEY]' \
 -d '{
        "applicationID": "1",
        "campaignID": "1",
        "identifier": "an-example-identifier",
        "integrationID": "an-example-integrationID",
        "startDate": "2019-06-12T09:00:00Z",
        "expiryDate": "2019-06-13T09:00:00Z"
}'
```
{% endtab %}
{% tab Custom Attributes %}
##### Example Three: Custom Attributes

Custom attributes can also be passed directly as long as they are notated with a dot prefix and still wrapped in a string as shown below.

```bash
curl https://demo.talon.one/v2/integration/braze/createcoupon \
 -X POST \
 -H 'Authorization: ApiKey-v1 [YOUR_API_KEY]' \
 -d '{
        "applicationID": "1",
        "campaignID": "1",
        "identifier": "an-example-identifier"
        ".stringAtrrName": "examplestring",
        ".listOfNumbers": "[1,2,3,4,5,6,7,8,9,10]",
    }'
```
{% endtab %}
{% endtabs %}

## Using Talon.One in Your Braze Campaign

To trigger the Talon.One coupon creation event, use Braze's [Connected Content feature]({{site.baseurl}}/user_guide/personalization_and_dynamic_content/connected_content/about_connected_content/). However, the coupon creation event can still be used in any Campaign message body or any canvas message body.

Place the code snippets below in your message body to configure your Campaign or Canvas to call Talon.One coupons into your message. 

{% alert tip %}
You can use Talon.One Coupon Codes in any Braze messaging channel (email, in-app message, push, Content Cards, News Feed) using any of our tools (Canvas or Campaigns). Get creative!
{% endalert %}

#### Step One: Add the Connected Content Tag to the Body of Your Message

{% raw %}
```liquid
{% connected_content %}

```
{% endraw %}

{% alert tip %}
You can access Braze Attributes by using Liquid tags (e.g. {% raw %} {{${user_id}} {% endraw %} to pass the user id).
{% endalert %}

#### Step Two: Add the URL to the createCoupon Endpoint of Your Talon.One Deployment

{% raw %}
```liquid
{% connected_content https://[YOUR_SUBDOMAIN].talon.one/v2/integration/braze/createcoupon %}

```
{% endraw %}

#### Step Three: Add the Authorization Header and the POST Method of the Request

{% raw %}
```liquid
{% connected_content https://[YOUR_SUBDOMAIN].talon.one/v2/integration/braze/createcoupon

:headers {
  "authorization": "ApiKey-v1 [YOUR_API_KEY]"
 } 
:method post %}
```
{% endraw %}

{% alert important %}
Further details on how to generate a session token can be found [here](https://developers.talon.one/Management-API/Authentication).
{% endalert %}

#### Step Four: Add the Body of the Request Containing the Coupon Code Specs Mentioned Above

{% raw %}
```liquid
{% connected_content https://[YOUR_SUBDOMAIN].talon.one/v2/integration/braze/createcoupon

:headers {
  "authorization": "ApiKey-v1 [YOUR_API_KEY]"
 } 
:method post 
:body applicationID=[YOUR_APPLICATION_ID]&campaignID=[YOUR_CAMPAIGN_ID]&identifier={{campaign.${message_api_id}}}&integrationID={{${user_id}}}
:content_type application/json 
%}
```
{% endraw %}

{% alert important %}
The **identifier** parameter is necessary to prevent the creation of multiple coupons for one message, and each paramter should be separated with an "&" as shown above.
{% endalert %}

#### Step Five: Storing the Talon.One Result

Add the **save** parameter at the end to store the Talon.One response as a Braze variable. In the example below, the Talon.One response is being saved in a variable name _result_.

{% raw %}
```liquid
{% connected_content https://[YOUR_SUBDOMAIN].talon.one/v2/integration/braze/createcoupon

:headers {
  "authorization": "ApiKey-v1 [YOUR_API_KEY]"
 } 
:method post 
:body applicationID=[YOUR_APPLICATION_ID]&campaignID=[YOUR_CAMPAIGN_ID]&identifier={{campaign.${message_api_id}}}&integrationID={{${user_id}}}
:content_type application/json 
:save result
%}
```
{% endraw %}

#### Step Six: Show the Value of the Code in the Message

Use a [liquid tag]({{site.baseurl}}/user_guide/personalization_and_dynamic_content/liquid/overview/) to show the value of the generated code in the message.

{% raw %}
```liquid
{% connected_content https://[YOUR_SUBDOMAIN].talon.one/v2/integration/braze/createcoupon

:headers {
  "authorization": "ApiKey-v1 [YOUR_API_KEY]"
 } 
:method post 
:body applicationID=[YOUR_APPLICATION_ID]&campaignID=[YOUR_CAMPAIGN_ID]&identifier={{campaign.${message_api_id}}}&integrationID={{${user_id}}}
:content_type application/json 
:save result
%}

{{result.value}}
```
{% endraw %}

{% alert tip %}
You can acess the coupon code with `{% raw %} {{result.value}} {% endraw %}` as shown above, which will return the generated value similar to `44D4-U4PL`. 

You can also access the entire response from Talon.One by accessing `{% raw %} {{result}} {% endraw %}` directly, which will look similar to `{"id"=>1548040, "value"=>"44D4-U4PL", "__http_status_code__"=>200}`.
{% endalert %}

# Troubleshooting

Ensure your Connected Content syntax is correct (as in, using the right Liquid tags for either a Canvas or Campaign, as well as referencing the right value in the `json` response).

Be aware of the 500 messages/minute rate limit that you would want to implement into the Braze Campaign/Canvas. If the rate limit is not respected, it cannot be guaranteed that every code will be generated, nor that the response will be there in time.