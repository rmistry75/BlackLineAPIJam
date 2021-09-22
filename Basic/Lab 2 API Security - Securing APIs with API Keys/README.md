# API Security : Securing APIs with API Keys

*Duration : 15 mins*

*Persona : API Team*

# Use case

You have an API proxy that you want to secure, so that you can manage access and perform business logic based on the client making the call.  In addition to authorizing users, you want to know which developer app is making calls so you can leverage that data to customize your API behavior based on the entitlement level of the caller, or even the specific caller.  You also would like to be able to see who is calling your API proxies in your Analytics dashboards.

# How can Apigee Edge help?

The [Verify API Key Policy](http://docs.apigee.com/api-services/reference/verify-api-key-policy) in Edge authenticates that the call is coming from an approved application in a valid state.  App developers who wish to access secure operations must request [API keys](https://docs.apigee.com/api-platform/security/api-keys) for their apps via the developer portal.

In addition to authenticating requests, the [Verify API Key Policy](https://docs.apigee.com/api-platform/reference/policies/verify-api-key-policy) provides context about the app making the call.  This context can be used to apply policies such as quota enforcement or routing based on the client app.  Upon successful verification of the API Key, the API Context is populated with details about the app, developer, and API product associated with the call.  This data can be used for applying business logic as well as gaining business insights through analytics.

In this lab, you will protect an existing API proxy with the [Verify API Key Policy](https://docs.apigee.com/api-platform/reference/policies/verify-api-key-policy) and use the trace tool to see the policy in action.  To accomplish this you will modify an existing API proxy to add a security policy to handle the authorization.  

# Pre-requisites

For this lab, you will need an API proxy that is not currently secured.  If you do not have an API proxy available for this lab, revisit Lab 1 "API Design : Create a Reverse Proxy from OpenAPI Spec" and then return here to complete these steps.

# Instructions

## Choose (and invoke) an API Proxy to secure

* Go to [https://apigee.com/edge](https://apigee.com/edge) and log in. This is the Edge management UI

* Select **Develop → API Proxies**

![image alt text](./media/image_0.png)

* Select the **{Your-Initials}-Employees-API** proxy that you created in Lab 1.

![image alt text](./media/image_1.png)

* Verify that the API proxy is deployed to an environment from the **Overview** page.  Environment(s) to which the selected revision of the API proxy is deployed will be indicated by a green circle.  If it is not deployed, click an environment from the "Deployment" pull-down to deploy the API proxy to that environment.

![image alt text](./media/image_2.png)

* Verify that you can successfully make calls using the built-in trace tool.

* Click the **Trace** tab near the top of the window.

* The Trace view allows you to initiate tracing for up to 10 minutes, during which time all requests (regardless of whether they are sent from the trace tool or any other client) will be captured and their traces made visible to you.

* Click **Start Trace Session** to begin a trace session.

* Click **Send** to send a request.  If your API proxy requires query parameters add them here prior to sending.

* You should see a successful 2xx response for your API Call (it may take a few seconds for the trace results to appear)

![image alt text](./media/image_3.png)

## Add a Verify API Key Policy

* Menu: **Develop > API Proxies**

* Open your API proxy and click the **Develop** tab (in the upper right area of the page) to see the flow editor (you may have to move the panes to see the full request and response flow lines). Make sure the the "PreFlow" Proxy Endpoint is selected in the left side pane.

* Click **+Step** on the request flow.

![image alt text](./media/image_4.png)

* Select *Verify API Key* policy from the *Security* section of the list. Name the policy "VAK-VerifyKey" as shown below.

* Click **Add**

![image alt text](./media/image_5.png)

* This is the first policy we've added. However, if we had other policies then we could drag the VerifyKey policy to the leftmost position to ensure it executes first.

* With the *Verify API Key* policy selected, you can see its configuration (the default policy configuration is below).  Note that the API Key is being retrieved from the context as the variable *request.queryparam.apikey*.  This is the default but the policy can be configured to retrieve the key from any parameter key you prefer. See the [help docs](https://docs.apigee.com/api-platform/reference/policies/verify-api-key-policy) to learn the syntax for retrieving the key from other parameters.

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <VerifyAPIKey async="false" continueOnError="false" enabled="true" name="VAK-VerifyKey">
    <DisplayName>VAK-VerifyKey</DisplayName>
     <Properties/>
    <APIKey ref="request.queryparam.apikey"/>
</VerifyAPIKey>
```

* **Save** the API proxy.

* Click the **Trace** tab near the top of the window.

* Click **Start Trace Session** to begin a trace session.

* Click **Send** to send a request.  

* You should see a 401 (unauthorized) response for your API Call because the API proxy was expecting an API Key as a query parameter.  See the trace session below

* NOTE: In Lab 4, you will get an API Key that will allow you to make this API call successfully.

![image alt text](./media/image_6.png)

## Update the API Spec

We can update our API's documentation to reflect the new Verify API Key policy.

* Menu: **Develop > Specs > {Your Initials} Employee Backend**

* Scroll to the bottom and add the following lines. This specifies that an api key is required, it will be called apikey, and it will be found in the query parameters.

```
securityDefinitions:
   APIKeyQueryParam:
     type: apiKey
     in: query
     name: apikey
```

![image alt text](./media/image_7.png)

* Scroll back to the top and add the following lines. This specifies that the APIKeyQueryParam security definition is applied to all paths in the api.

```
security:
  - APIKeyQueryParam: []
```

![image alt text](./media/image_8.png)

# Lab Video

If you would rather watch a video that covers this topic, point your browser [here](https://youtu.be/3nUFCOgGlS8). (note: you only need to watch until the 3:00 minute mark for this specific lab).

# Quiz

1. Why is the Verify API Key policy typically found as the first policy in the Request PreFlow?  When might it be in a conditional PreFlow instead of the "All" PreFlow?

2. How would you configure the policy to get the API Key from a header called "Api-Key" instead of the default query parameter location?

# Summary

In this lab you learned how to protect your API proxy using the Verify API Key policy.  You implemented the policy and tested it using the built-in Trace Tool.

# References

* Link to Apigee docs page

    * Verify Api Key Policy [https://docs.apigee.com/api-platform/reference/policies/verify-api-key-policy](https://docs.apigee.com/api-platform/reference/policies/verify-api-key-policy)
    
    * Visualizing the flow of an API proxy [https://docs.apigee.com/api-platform/fundamentals/introduction-flow-variables#understandingflowvariablescope](https://docs.apigee.com/api-platform/fundamentals/introduction-flow-variables#understandingflowvariablescope)

# Rate this lab

How did you like this lab? Rate [here](https://goo.gl/forms/XxDqVtWHnyFdEChv1).

Now go to [Lab-3](../Lab%203%20API%20Publishing%20-%20API%20Products%20and%20Developer%20Portals)

