# Azure App Services 

## Overview

* Is a Pre-Configured web server that allows to serve web applications, APIs, mobile backend, etc. 
* It can run on a Linux based system or a Windows based system and has a stack of supported languages by OS
* Supports deploying and running containerized web apps, pulling images from Azure Container Registry.
* Supports auto-scale based on the usage of the web app. 
    * You can choose to scale vertically or horizontally according to rules. 
* It supports all major CI/CD services such as ADO, GitHub, etc. 
* It supports deployment slots. 
* You can deploy multiple apps in an Azure App Service.

## App Service Plans

An Azure App Service runs in an Azure App Service Plan which defines a set of compute resources for the app to run:

* Operating System (Windows, Linux)
* Region (West US, East US, etc.)
* Number of VM instances
* Size of VM instances (Small, Medium, Large)
* Pricing tier (Free, Shared, Basic, Standard, Premium, PremiumV2, PremiumV3, Isolated, IsolatedV2)
 
Pricing tier defines the features and price of the plan: 

* **Shared compute**: Free and Shared, the two base tiers, runs an app on the same Azure VM as other App Service apps, including apps of other customers. These tiers allocate CPU quotas to each app that runs on the shared resources, and the resources can't scale out.
* **Dedicated compute**: The Basic, Standard, Premium, PremiumV2, and PremiumV3 tiers run apps on dedicated Azure VMs. Only apps in the same App Service plan share the same compute resources. The higher the tier, the more VM instances are available to you for scale-out.
* **Isolated**: The Isolated and IsolatedV2 tiers run dedicated Azure VMs on dedicated Azure Virtual Networks. It provides network isolation on top of compute isolation to your apps. It provides the maximum scale-out capabilities.

### Scalability

All deployed apps and slots will run on all configured VM instances, so all apps and slots are scaled out together based on the autoscale settings.

## Authentication and authorization 

App Service provides out-of-the-box authentication with federated identity providers:


|Provider|Sign-in endpoint|How-To guidance|
|---|---|---|
|Microsoft identity platform|	/.auth/login/aad	|App Service Microsoft identity platform login|
|Facebook|	/.auth/login/facebook	|App Service Facebook login|
|Google|	/.auth/login/google	|App Service Google login|
|X|	/.auth/login/twitter	|App Service X login|
|Any OpenID Connect provider|	/.auth/login/<providerName>|App Service OpenID Connect login|
|GitHub	|/.auth/login/github	|App Service GitHub login|

When you enable one of this providers the endpoint becomes available for authentication and validation. 

### How it works

The module runs in a pipeline intercepting incoming requests and handling the following

* Authenticates users and clients with the specified identity provider
* Validates, stores, and refreshes OAuth tokens issued by the configured identity provider
* Manages the authenticated session
* Injects identity information into HTTP request headers

This module can be configured on the portal or by a configuration file, no changes to the app are required.

## Authentication flow

The flow is the same for all providers but you can choose to use the provider SDK therefore varying it slightly:

* Server-directed-flow or server flow is when the app delegates the federated sign in to App Service. (Without SDK)
* Client-directed-flow or client flow is when the application handles the federated sign-in and passes the token to App Service. (Using SDK)

|Step|	Without provider SDK	|With provider SDK|
|---|---|---|
|Sign user in| Redirects client to /.auth/login/<provider>.|	Client code signs user in directly with provider's SDK and receives an authentication token. For information, see the provider's documentation.|
|Post-authentication	|Provider redirects client to /.auth/login/<provider>/callback.|	Client code posts token from provider to /.auth/login/<provider> for validation.|
|Establish authenticated session	|App Service adds authenticated cookie to response.|	App Service returns its own authentication token to client code.|
|Serve authenticated content|	Client includes authentication cookie in subsequent requests (automatically handled by browser).|	Client code presents authentication token in X-ZUMO-AUTH header (automatically handled by Mobile Apps client SDKs).

## Authorization behavior
 
Using the portal you can configure the following behavior

* **Allow unauthenticated requests**; thus deferring authorization of unauthenticated traffic to the application 
* **Require authentication** thus rejecting all incoming requests and redirecting to /.auth/login/<provider>


