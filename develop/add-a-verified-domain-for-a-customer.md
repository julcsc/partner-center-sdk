---
title: Add a verified domain for a customer
description: Learn how to add a verified domain to the list of approved domains for a customer in Partner Center. Do so using Partner Center APIs and REST APIs.
ms.date: 05/21/2019
ms.service: partner-dashboard
ms.subservice:  partnercenter-sdk
---

# Add a verified domain to the list of approved domains for an existing customer 

**Applies to**: Partner Center | Partner Center operated by 21Vianet | Partner Center for Microsoft Cloud Germany | Partner Center for Microsoft Cloud for US Government

How to add a verified domain to the list of approved domains for an existing customer.

## Prerequisites

- You must be a Partner who is a domain registrar.

- Credentials as described in [Partner Center authentication](partner-center-authentication.md). This scenario supports authentication with both standalone App and App+User credentials.

- A customer ID (`customer-tenant-id`). If you don't know the customer's ID, you can look it up in the Partner Center [dashboard](https://partner.microsoft.com/dashboard). Select **CSP** from the Partner Center menu, followed by **Customers**. Select the customer from the customer list, then select **Account**. On the customer’s Account page, look for the **Microsoft ID** in the **Customer Account Info** section. The Microsoft ID is the same as the customer ID  (`customer-tenant-id`).

## Adding a verified domain

If you are a Partner who is a domain registrar, you can use the `verifieddomain` API to POST a new [Domain](#domain) resource to the list of domains for an existing customer. To do this, identify the customer using their CustomerTenantId. Specify a value for the VerifiedDomainName property. Pass a [Domain](#domain) resource in the Request with the required Name, Capability, AuthenticationType, Status, and VerificationMethod properties included. To specify that the new [Domain](#domain) is a federated domain, set the AuthenticationType property in the [Domain](#domain) resource to `Federated`, and include a [DomainFederationSettings](#domain-federation-settings) resource in the Request. If the method is successful, the Response will include a [Domain](#domain) resource for the new verified domain.

### Custom verified domains

When adding a custom verified domain, a domain that isn't registered on **onmicrosoft.com**, you must set the [CustomerUser.immutableId](user-resources.md#customeruser) property to a unique ID value for the customer you are adding the domain for. This unique identifier is required during the validation process when the domain is being verified. For more information about customer user accounts, see [create user accounts for a customer](create-user-accounts-for-a-customer.md).

## REST request

### Request syntax

| Method | Request URI                                                                                        |
|--------|----------------------------------------------------------------------------------------------------|
| POST   | [*{baseURL}*](partner-center-rest-urls.md)/v1/customers/{CustomerTenantId}/verifieddomain HTTP/1.1 |

#### URI parameter

Use the following query parameter to specify the customer you are adding a verified domain for.

| Name                   | Type     | Required | Description                                                                                                                                            |
|------------------------|----------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| CustomerTenantId | guid | Y        | The value is a GUID formatted **CustomerTenantId** that allows you to specify a customer. |

### Request headers

For more information, see [Partner Center REST headers](headers.md).

### Request body

This table describes the required properties in the request body.

| Name                                                  | Type   | Required                                      | Description                                                |
|-------------------------------------------------------|--------|-----------------------------------------------|--------------------------------------------------------|
| VerifiedDomainName                                    | string | Yes                                           | The verified domain name. |
| [Domain](#domain)                                     | object | Yes                                           | Contains the domain information. |
| [DomainFederationSettings](#domain-federation-settings) | object | Yes (If AuthenticationType = `Federated`)     | The domain federation settings to be used if the domain is a `Federated` domain and not a `Managed` domain. |

### Domain

This table describes the required and optional **Domain** properties in the request body.

| Name               | Type                                     | Required | Description                                                                                                                                                                                                     |
|--------------------|------------------------------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AuthenticationType                                    | string           | Yes      | Defines whether the domain is a `Managed` domain or a `Federated` domain. Supported values: `Managed`, `Federated`.|
| Capability                                            | string           | Yes      | Specifies the domain capability. For example, `Email`.                  |
| IsDefault                                             | nullable boolean | No       | Indicates whether the domain is the default domain for the tenant. Supported values: `True`, `False`, `Null`.        |
| IsInitial                                             | nullable boolean | No       | Indicates whether the domain is an initial domain. Supported values: `True`, `False`, `Null`.                       |
| Name                                                  | string           | Yes      | The domain name.                                                          |
| RootDomain                                            | string           | No       | The name of the root domain.                                              |
| Status                                                | string           | Yes      | The domain status. For example, `Verified`. Supported values:  `Unverified`, `Verified`, `PendingDeletion`.                               |
| VerificationMethod                                    | string           | Yes      | The domain verification method type. Supported values: `None`, `DnsRecord`, `Email`.                                    |

### Domain federation settings

This table describes the required and optional **DomainFederationSettings** properties in the request body.

| Name   | Type   | Required | Description                                                  |
|--------|--------|----------|--------------------------------------------------------------|
| ActiveLogOnUri                         | string           | No      | The logon URI used by rich clients. This property is the Partner's STS Auth URL. |
| DefaultInteractiveAuthenticationMethod | string           | No      | Indicates the default authentication method that should be used when an application requires the user to have interactive login. |
| FederationBrandName                    | string           | No      | The federation brand name.        |
| IssuerUri                              | string           | Yes     | The name of the issuer of the certificates.                        |
| LogOffUri                              | string           | Yes     | The logoff URI. This property describes the federated domain sign-out URI.        |
| MetadataExchangeUri                    | string           | No      | The URL that specifies the metadata exchange endpoint used for authentication from rich client applications. |
| NextSigningCertificate                 | string           | No      | The certificate used for the coming future by the ADFS V2 STS to sign claims. This property is a base64 encoded representation of the certificate. |
| OpenIdConnectDiscoveryEndpoint         | string           | No      | The OpenID Connect Discovery Endpoint of the federated IDP STS. |
| PassiveLogOnUri                        | string           | Yes     | The logon URI used by older passive Clients. This property is the address to send federated sign-in requests. |
| PreferredAuthenticationProtocol        | string           | Yes     | The format for the authentication token. For example, `WsFed`. Supported values: `WsFed`, `Samlp` |
| PromptLoginBehavior                    | string           | Yes     | The prompt login behavior type.  For example, `TranslateToFreshPasswordAuth`. Supported values: `TranslateToFreshPasswordAuth`, `NativeSupport`, `Disabled` |
| SigningCertificate                     | string           | Yes     | The certificate currently used by the ADFS V2 STS to sign claims. This property is a base64 encoded representation of the certificate. |
| SigningCertificateUpdateStatus         | string           | No      | Indicates the update status of the Signing certificate. |
| SigningCertificateUpdateStatus         | nullable boolean | No      | Indicates whether the IDP STS supports MFA. Supported values: `True`, `False`, `Null`.|

### Request example

```http
POST https://api.partnercenter.microsoft.com/v1/customers/{CustomerTenantId}/verifieddomain HTTP/1.1
Authorization: Bearer <token>
Accept: application/json, text/plain, */*
MS-RequestId: 312b044d-dc41-4b37-c2d5-7d27322d9654
MS-CorrelationId: 7cb67bb7-4750-403d-cc2e-6bc44c52d52c
Content-Type: application/json;charset=utf-8
X-Locale: "en-US"

{
    "VerifiedDomainName": "Example.com",
    "Domain": {
        "AuthenticationType": "Federated",
        "Capability": "Email",
        "IsDefault": Null,
        "IsInitial": Null,
        "Name": "Example.com",
        "RootDomain": null,
        "Status": "Verified",
        "VerificationMethod": "None"
    },
    "DomainFederationSettings": {
        "ActiveLogOnUri": "https://sts.microsoftonline.com/FederationPassive/",
        "DefaultInteractiveAuthenticationMethod": "http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password",
        "FederationBrandName": "FederationBrandName",
        "IssuerUri": "Example.com",
        "LogOffUri": "https://sts.microsoftonline.com/FederationPassive/",
        "MetadataExchangeUri": null,
        "NextSigningCertificate": null,
        "OpenIdConnectDiscoveryEndpoint": "https://sts.contoso.com/adfs/.well-known/openid-configuration",
        "PassiveLogOnUri": "https://sts.microsoftonline.com/Trust/2005/UsernameMixed",
        "PreferredAuthenticationProtocol": "WsFed",
        "PromptLoginBehavior": "TranslateToFreshPasswordAuth",
        "SigningCertificate": <Certificate Signature goes here>,
        "SigningCertificateUpdateStatus": null,
        "SupportsMfa": true
    }
}
```

## REST response

If successful, this API returns a [Domain](#domain) resource for the new verified domain.

### Response success and error codes

Each response comes with an HTTP status code that indicates success or failure and additional debugging information. Use a network trace tool to read this code, error type, and additional parameters. For the full list, see [Partner Center REST error codes](error-codes.md).

### Response example

```http
HTTP/1.1 201 Created
Content-Length: 206
Content-Type: application/json; charset=utf-8
MS-CorrelationId: 7cb67bb7-4750-403d-cc2e-6bc44c52d52c
MS-RequestId: 312b044d-dc41-4b37-c2d5-7d27322d9654
Date: Tue, 14 Feb 2017 20:06:02 GMT

{
    "authenticationType": "federated",
    "capability": "email",
    "isDefault": false,
    "isInitial": false,
    "name": "Example.com",
    "status": "verified",
    "verificationMethod": "dns_record"
}
```
