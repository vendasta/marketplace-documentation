title Vendor OIDC SSO

participant OP [Vendasta] AS OP
participant RP [Vendor] AS RP

note right of OP
User clicks on product or link (external to OAuth spec)
end note
OP->RP: (1) Redirect to Product Entry URL w/ <accountId> injected [client side]
note right of RP
Vendor generates Authorization URL, 
appends account_id to Authorization URL params
*Optionally encodes nextUrl in state param
end note
RP->OP:(2) RP redirects the User Agent to the OpenID Provider(OP) [client side]
note left of OP
User is Authenticated
end note
OP->RP:(3) OP sends authorization code to RP redirect_uri [client side]
RP->OP:(4) RP calls token endpoint w/authorization code & client secret [back channel]
OP-->RP:(5) OP responds with ID Token & Access Token [back channel]
opt Vendor retrieves User Info
RP->OP:(6) RP sends request with Access Token to UserInfo Endpoint [back channel]
OP-->RP:(7) UserInfo Endpoint returns applicable User Claims [back channel]