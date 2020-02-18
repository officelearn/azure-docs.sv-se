---
title: Anspråk matchare i anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du använder anspråk matchare i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/17/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4434c877f69391f5dc5926c6aed07049ba46b7b7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425654"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Om anspråk matchare i Azure Active Directory B2C anpassade principer

Anspråk matchare i Azure Active Directory B2C (Azure AD B2C) [anpassade principer](custom-policy-overview.md) ger Sammanhangs information om en auktoriseringsbegäran, till exempel princip namn, KORRELATIONS-ID för begäran, användar gränssnitts språk med mera.

Om du vill använda en anspråks lösare i ett indata-eller utgående anspråk definierar du en sträng **claimType**, under elementet [ClaimsSchema](claimsschema.md) , och anger sedan **DefaultValue** till anspråks lösa ren i elementet indata-eller utdata-anspråk. Azure AD B2C läser värdet för anspråks matcharen och använder värdet i den tekniska profilen.

I följande exempel definieras en anspråks typ med namnet `correlationId` med en `string`s **datatyp** .

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

I den tekniska profilen mappar du anspråks matcharen till anspråks typen. Azure AD B2C fyller i värdet för anspråks matcharen `{Context:CorrelationId}` till anspråks `correlationId` och skickar anspråket till den tekniska profilen.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typer av anspråks matchare

I följande avsnitt listas tillgängliga anspråks lösningar.

### <a name="culture"></a>Kultur

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Culture: LanguageName} | Den två bokstävernas ISO-kod för språket. | en |
| {Culture: LCID}   | LCID för språk koden. | 1033 |
| {Culture: RegionName} | ISO-koden för den två bokstaven för regionen. | USA |
| {Culture: RFC5646} | Språk koden RFC5646. | sv-SE |

### <a name="policy"></a>Princip

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Princip: PolicyId} | Princip namnet för den förlitande parten. | B2C_1A_signup_signin |
| {Princip: RelyingPartyTenantId} | Klient-ID för den förlitande part principen. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | Klientens objekt-ID för den förlitande part principen. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | Klient-ID för förtroende ramverket. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |Parametern `acr_values` frågesträng. | Ej tillämpligt |
| {OIDC:ClientId} |Parametern `client_id` frågesträng. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |Parametern `domain_hint` frågesträng. | facebook.com |
| {OIDC: LoginHint} |  Parametern `login_hint` frågesträng. | someone@contoso.com |
| {OIDC:MaxAge} | `max_age`. | Ej tillämpligt |
| {OIDC:Nonce} |Parametern `Nonce` frågesträng. | defaultNonce |
| {OIDC: prompt} | Parametern `prompt` frågesträng. | inloggning |
| {OIDC:Resource} |Parametern `resource` frågesträng. | Ej tillämpligt |
| {OIDC: omfång} |Parametern `scope` frågesträng. | OpenID |

### <a name="context"></a>Kontext

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Context: BuildNumber} | Versionen av ID Experience Framework (versions nummer).  | 1.0.507.0 |
| {Context: CorrelationId} | Korrelations-ID  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Datum tid i UTC.  | 10/10/2018 12:00:00 PM |
| {Context: DeploymentMode} |Princip distributions läget.  | Produktion |
| {Context: IPAddress} | Användarens IP-adress. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Parametrar som inte är protokoll

Alla parameter namn som ingår i en OIDC-eller OAuth2-begäran kan mappas till ett anspråk i användar resan. Begäran från programmet kan till exempel innehålla en frågesträngparametern med namnet `app_session`, `loyalty_number`eller en anpassad frågesträng.

| Begär | Beskrivning | Exempel |
| ----- | ----------------------- | --------|
| {OAUTH-KV: campaignId} | En frågesträngparametern. | hawaii |
| {OAUTH-KV: app_session} | En frågesträngparametern. | A3C5R |
| {OAUTH-KV:loyalty_number} | En frågesträngparametern. | 1234 |
| {OAUTH-KV: valfri anpassad frågesträng} | En frågesträngparametern. | Ej tillämpligt |

### <a name="oauth2"></a>OAuth2

| Begär | Beskrivning | Exempel |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Åtkomsttoken. | Ej tillämpligt |


### <a name="saml"></a>SAML

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {SAML: AuthnContextClassReferences} | Värdet för `AuthnContextClassRef` element från SAML-begäran. | urn: Oasis: Names: TC: SAML: 2.0: AC: klasser: PasswordProtectedTransport |
| {SAML: NameIdPolicyFormat} | Attributet `Format` från `NameIDPolicy`-elementet i SAML-begäran. | urn: Oasis: Names: TC: SAML: 1.1: NameID-format: emailAddress |
| {SAML: Issuer} |  SAML-`Issuer` element värde för SAML-begäran.| https://contoso.com |
| {SAML: AllowCreate} | Värdet för `AllowCreate`-attributet från `NameIDPolicy`-elementet i SAML-begäran. | True |
| {SAML: ForceAuthn} | Värdet för `ForceAuthN`-attributet från `AuthnRequest`-elementet i SAML-begäran. | True |
| {SAML: ProviderName} | Värdet för `ProviderName`-attributet från `AuthnRequest`-elementet i SAML-begäran.| Contoso.com |

## <a name="using-claim-resolvers"></a>Använda anspråks matchare 

Du kan använda anspråk matchare med följande element: 

| Objekt | Element | Inställningar |
| ----- | ----------------------- | --------|
|Application Insights teknisk profil |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md) teknisk profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md) teknisk profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID Connect](openid-connect-technical-profile.md) , teknisk profil| `InputClaim`, `OutputClaim`| 1, 2|
|Teknisk profil för [anspråks omvandling](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Teknisk profil för [RESTful-Provider](restful-technical-profile.md)| `InputClaim`| 1, 2|
|[SAML2](saml-technical-profile.md) teknisk profil| `OutputClaim`| 1, 2|
|[Egen, kontrollerad](self-asserted-technical-profile.md) teknisk profil| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile) teknisk profil| `OutputClaim`| 2 |

Autentiseringsinställningar 
1. `IncludeClaimResolvingInClaimsHandling` metadata måste anges till `true`.
1. Attributet indata-eller utgående anspråk `AlwaysUseDefaultValue` måste anges till `true`.

## <a name="claim-resolvers-samples"></a>Exempel på anspråks lösare

### <a name="restful-technical-profile"></a>RESTful teknisk profil

I en [RESTful](restful-technical-profile.md) teknisk profil kanske du vill skicka användar språket, princip namnet, omfattningen och klient-ID: t. Baserat på dessa anspråk kan REST API köra anpassad affärs logik, och om det behövs kan du generera ett lokaliserat fel meddelande.

I följande exempel visas en RESTful teknisk profil med det här scenariot:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Direkt inloggning

Med hjälp av anspråks matchare kan du fylla i inloggnings namnet eller dirigera inloggningen till en speciell social identitetsprovider, till exempel Facebook, LinkedIn eller en Microsoft-konto. Mer information finns i [Konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Anpassning av dynamiskt gränssnitt

Med Azure AD B2C kan du skicka frågesträngs parametrar till definitions slut punkter för HTML-innehåll för att dynamiskt återge sid innehållet. Detta gör det till exempel möjligt att ändra bakgrunds bilden på Azure AD B2C registrerings-eller inloggnings sida baserat på en anpassad parameter som du skickar från ditt webb program eller mobil program. Mer information finns i [Konfigurera användar gränssnittet dynamiskt genom att använda anpassade principer i Azure Active Directory B2C](custom-policy-ui-customization.md). Du kan också lokalisera HTML-sidan baserat på en språk parameter, eller så kan du ändra innehållet baserat på klient-ID: t.

Följande exempel skickar i frågesträngparametern med namnet **campaignId** med värdet `hawaii`, en **språkkod** `en-US`och en **app** som representerar klient-ID:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Därför skickar Azure AD B2C ovanstående parametrar till sidan HTML-innehåll:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Innehålls definition

I en [ContentDefinition](contentdefinitions.md) `LoadUri`kan du skicka anspråk matchare för att hämta innehåll från olika platser, baserat på de parametrar som används. 

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Application Insights teknisk profil

Med Azure Application insikter och anspråks matchare kan du få insikter om användar beteendet. I Application Insights teknisk profil skickar du inloggade anspråk som är sparade för att Azure Application insikter. Mer information finns i [spåra användar beteende i Azure AD B2C-transporter med hjälp av Application Insights](analytics-with-application-insights.md). I följande exempel skickas princip-ID: t, korrelations-ID, språk och klient-ID till Azure Application insikter.

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Princip för förlitande part

I en teknisk profil för [förlitande part](relyingparty.md) , kanske du vill skicka klient-ID eller KORRELATIONS-ID till det förlitande part programmet i JWT. 

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
