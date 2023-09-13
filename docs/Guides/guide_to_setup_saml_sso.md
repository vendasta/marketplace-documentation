# Guide to Setup SAML SSO

This guide aims to provide a step-by-step walkthrough for configuring [SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-core-2.0-os.pdf) Single Sign-On (SSO) using Vendasta as the Identity Provider (IDP). SAML, which stands for Security Assertion Markup Language, is a widely used standard for enabling single sign-on and identity federation across different web applications. Before we dive into the configuration process, let's understand some key technical terms related to SAML.

## Technology Review:
### Key Terms:

1. **Service Provider (SP):**

    - The Service Provider is the application or system that users want to access after a successful SAML SSO. In our case, it's your marketplace application.

2. **Identity Provider (IDP):**

    - The Identity Provider is the system that authenticates users and provides identity information to the Service Provider. Vendasta will serve as your Identity Provider in this setup.

3. **Metadata:**
    - Metadata in the context of SAML (Security Assertion Markup Language) refers to XML documents that contain important configuration information about either the Identity Provider (IDP) or the Service Provider (SP). These documents serve as a way for these entities to exchange critical information required for successful SAML-based single sign-on (SSO).

- **IDP Metadata:** The IDP metadata typically contains information about the Identity Provider, including its entity ID, SSO and Single Logout Service URLs, and its public key for signing SAML assertions. The Service Provider can consume this metadata to configure its SAML SSO settings correctly. IDP Metadata will look like the below example:
```xml
<EntityDescriptor xmlns="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://sso-api-demo.apigateway.co/saml2/spc/oWw1OqV" validUntil="2024-09-11T04:57:03.170901138Z" cacheDuration="31536000000000000">
  <IDPSSODescriptor xmlns="urn:oasis:names:tc:SAML:2.0:metadata" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <KeyDescriptor use="signing">
      <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
        <X509Data xmlns="http://www.w3.org/2000/09/xmldsig#">
          <X509Certificate xmlns="http://www.w3.org/2000/09/xmldsig#">MIIDAjCCAeqgAwIBAgIQPrA/JUkXbzdXMYLJ3lpqRMSEwHwYDVQQDExhWZW5kYXN0YSBJRFAgQ2VydGlmaWNhdGUwHhcNMjMwOTExMTA1MTI3WhcNMjgwOTExMTA1MTI3WjAjMSEwHwYDVQQDExhWZW5kYXN0YSBJRFAgQ2VydGlmaWNhdGUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC1hUlVGu2y/rG8b1O5ccrynrsy0uxUBB9TsOZBeC9nLhNB6NqE7AvqMxRYg1xIC8l+nApA4xTSH6L8p0EETjb8aiw26XfjWDVX3p+ZJVAatwX5r81gjQm3jYWuuO6YRVymp+JLjvrcidk1YrWr4okqQ8xrngIITR8OJ4gIvwnRttovvNgSJNgJ2A34alcNNJzDcQTViGSiYtnnztCe9f3S9PcVEXljyWT5LTUOLrewKInhpuBgiUgEzVNFsA0T7RZk0MahnkHmB8xCiVVpe8vA13/XKoDxm900cdx9UL60ceeJ7UXw1Tlj9vnf6ouh0Ll+EKEAE3vG0VUn9NLiijW5AgMBAAGjMjAwMA8GA1UdEwEB/wQFMAMBAf8wHQYDVR0OBBYEFFEmAgF7XMFD/mYRTd9mjl7v7vpMMA0GCSqGSIb3DQEBCwUAA4IBAQAkjnwlWbPqFtBUPYkJ/lmAIcqZlpvAG96+BsABBxPXQ3qzEMpKspBcUXDyEeE+br1rsiTQdCiSQBQOPKknzht/IN6Dm5+0kSnJn1yRdHgsAmceGKGAV83QTIJtifSMnojwr8SyJwILSSkzJ672ilcGhYnrrFQkri91PMab+XrcGfmjzbnfvy85fwJnWUpE2hnvawnZc1YyR2Y6MERyP2V6H/H2Ma2PvZNUtmOOoo6AZx2tQq+UCbcYmLpKlzdTGM5SlrDviToBF+5rpQmUaCvk8jXJQQDXlNN+WxytL5/bet7MKWSSxckTs5SznkcX8HYdFw</X509Certificate>
        </X509Data>
      </KeyInfo>
    </KeyDescriptor>
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://sso-api-demo.apigateway.co/saml2/logout"></SingleLogoutService>
    <NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</NameIDFormat>
    <NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</NameIDFormat>
    <NameIDFormat>urn:oasis:names:tc:SAML:2.0:nameid-format:persistent</NameIDFormat>
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://sso-api-demo.apigateway.co/saml2/spc/oWw1OqV/auth"></SingleSignOnService>
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://sso-api-demo.apigateway.co/saml2/spc/oWw1OqV/auth"></SingleSignOnService>
  </IDPSSODescriptor>
</EntityDescriptor>
```
- **SP Metadata:** Similarly, the Service Provider can also have its own metadata document that describes its configuration, including its entity ID, ACS URL, and public key. The Identity Provider can use this metadata to configure its settings when establishing trust with the Service Provider. SP Metadata will look like the below example:
```xml
<md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" xmlns:ds="http://www.w3.org/2000/09/xmldsig#" entityID="IAMShowcase" validUntil="2025-12-09T09:13:31.006Z">
  <md:SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat>
    <md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</md:NameIDFormat>
    <md:AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://sptest.iamshowcase.com/acs" index="0" isDefault="true"/>
  </md:SPSSODescriptor>
</md:EntityDescriptor>
```
4. **Entity ID (Entity Identifier):**
    - Entity ID is a unique identifier for a service provider (SP) or identity provider (IDP) in the SAML ecosystem. It's often represented as a URL, which helps identify the participating parties in the SAML SSO process.

Simplified explanation of the SP-initiated flow of SAML using Vendasta as the Identity Provider (IDP):

![Alt text](./../../assets/images/guides/sso/sp-initiated-flow-diagram.png)

1. The process begins when the end user (usually an employee or customer) attempts to access a resource or application hosted by the Service Provider (SP). 

1. The SP generates a SAML authentication request, which is essentially an XML document. The SP sends this SAML request back to the user's web browser, which then acts as a messenger. 

1. The browser redirects itself to the Vendasta Identity Provider. At the IDP (Vendasta), the user's identity is verified. This can involve traditional username and password authentication or using Google Login.

1. Upon successful user authentication, Vendasta's IDP creates a SAML assertion. This is another XML document that contains details about the user, such as their identity and any additional attributes or roles.The SAML assertion is sent back to the user's browser, which then sends it back to the original SP. This relayed data essentially confirms that the user has been authenticated by Vendasta's IDP.

1. The SP receives the SAML assertion and validates its authenticity. It checks whether the assertion is signed correctly by the trusted IDP (Vendasta) and whether it hasn't been tampered with during transmission.

1. If the SAML assertion is valid and the user is indeed authenticated, the SP establishes a security context. This context serves as proof that the user is authorized to access the requested resource.

1. With the security context in place, the user can now request access to the specific resource, whether it's a web page or any other protected asset hosted by the SP.

1. The SP checks the security context and user permissions to determine whether the user should be granted access to the requested resource. If authorized, the user is given access, and they can use the resource as intended.

This completes the SP-initiated SAML flow using Vendasta as the IDP, allowing the user to seamlessly access the desired resource after successful authentication.

Now, let's proceed with the steps to configure SAML SSO with Vendasta as the IDP for your marketplace application.


## Step 1 - Navigate to Integration tab to Configure SSO

> [!NOTE]  
> Access Vendor Center by logging in directly in at https://vendors.vendasta.com, or navigate from Partner Center using the top navbar App icon beside your name.

> **Note**
> Text
