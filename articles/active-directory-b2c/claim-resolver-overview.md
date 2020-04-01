---
title: Anspråkslösare i anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du använder anspråkslösare i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c4bbd98682d964cfdf72031c7d6cb77cf42a809
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396074"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Om anspråkslösare i Azure Active Directory B2C-anpassade principer

Anspråkslösare i Azure Active Directory B2C (Azure AD B2C) [anpassade principer](custom-policy-overview.md) ger kontextinformation om en auktoriseringsbegäran, till exempel principnamnet, begäran om korrelations-ID, användargränssnittsspråk med mera.

Om du vill använda en anspråksmatchningsutlösare i ett indata- eller utdataanspråk definierar du en sträng **ClaimType**under elementet [ClaimsSchema](claimsschema.md) och ställer sedan in **DefaultValue** till anspråksmatcharen i indata- eller utdataanspråkselementet. Azure AD B2C läser värdet för anspråksmatcharen och använder värdet i den tekniska profilen.

I följande exempel definieras en `correlationId` med namnet anspråkstyp med en **DataType** av `string`.

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

I den tekniska profilen mappar du anspråkslösaren till anspråkstypen. Azure AD B2C fyller i värdet `{Context:CorrelationId}` på anspråksmatcharen i anspråket `correlationId` och skickar anspråket till den tekniska profilen.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typer av anspråksmatchningsstöd

I följande avsnitt listas tillgängliga anspråkslösare.

### <a name="culture"></a>Kultur

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Kultur:Språknamn} | De två bokstaven ISO-kod för språket. | en |
| {Kultur:LCID}   | LCID av språkkod. | 1033 |
| {Kultur:RegionNamn} | Regionens ISO-kod med två bokstäver. | USA |
| {Kultur:RFC5646} | Språkkoden för RFC5646. | sv-SE |

### <a name="policy"></a>Princip

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Princip:PolicyId} | Det förlitande partiprincipnamnet. | B2C_1A_signup_signin |
| {Princip:RelyingPartyTenantId} | Klient-ID för den förlitande partens princip. | your-tenant.onmicrosoft.com |
| {Princip:TenantObjectId} | Klientobjekt-ID för den förlitande partens princip. | 00000000-0000-0000-0000-000000000000 |
| {Princip:TrustFrameworkTenantId} | Klient-ID för förtroenderamverket. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |Parametern `acr_values` för frågesträngen. | Ej tillämpligt |
| {OIDC:ClientId} |Parametern `client_id` för frågesträngen. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |Parametern `domain_hint` för frågesträngen. | facebook.com |
| {OIDC:LoginHint} |  Parametern `login_hint` för frågesträngen. | someone@contoso.com |
| {OIDC:MaxAge} | Den `max_age`. | Ej tillämpligt |
| {OIDC:Nonce} |Parametern `Nonce` för frågesträngen. | standardIngynce |
| {OIDC:Password}| [Autentiseringsuppgifterna för resursägares lösenord flödar](ropc-custom.md) användarens lösenord.| lösenord1| 
| {OIDC:Prompt} | Parametern `prompt` för frågesträngen. | inloggning |
| {OIDC:RedirectUri} |Parametern `redirect_uri` för frågesträngen. | https://jwt.ms |
| {OIDC:Resource} |Parametern `resource` för frågesträngen. | Ej tillämpligt |
| {OIDC:Scope} |Parametern `scope` för frågesträngen. | Openid |
| {OIDC:Användarnamn}| [Resursägarens lösenordsautentiseringsuppgifter flödar](ropc-custom.md) användarens användarnamn.| emily@contoso.com| 

### <a name="context"></a>Kontext

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Kontext:BuildNumber} | Identity Experience Framework-versionen (byggnummer).  | 1.0.507.0 |
| {Context:CorrelationId} | Korrelations-ID.  | 00000000-0000-0000-0000-000000000000 |
| {Sammanhang:DateTimeInUtc} |Datumtiden i UTC.  | 2018-10-10 12:00:00 |
| {Kontext:DeploymentMode} |Principdistributionsläget.  | Produktion |
| {Kontext:IPAddress} | Användarens IP-adress. | 11.111.111.11 |
| {Sammanhang:KMSI} | Anger om [kryssrutan Behåll mig inloggad](custom-policy-keep-me-signed-in.md) är markerad. |  true |

### <a name="non-protocol-parameters"></a>Parametrar som inte är protokollsst.

Alla parameternamn som ingår som en del av en OIDC- eller OAuth2-begäran kan mappas till ett anspråk i användarfärden. Begäran från programmet kan till exempel innehålla en frågesträngparameter med namnet `app_session`, `loyalty_number`eller en anpassad frågesträng.

| Begär | Beskrivning | Exempel |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | En frågesträngparameter. | Hawaii |
| {OAUTH-KV:app_session} | En frågesträngparameter. | A3C5R |
| {OAUTH-KV:loyalty_number} | En frågesträngparameter. | 1234 |
| {OAUTH-KV:någon anpassad frågesträng} | En frågesträngparameter. | Ej tillämpligt |

### <a name="oauth2"></a>OAuth2

| Begär | Beskrivning | Exempel |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Åtkomsttoken. | Ej tillämpligt |


### <a name="saml"></a>SAML

| Begär | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {SAML:AuthnContextClassReferences} | Elementvärdet `AuthnContextClassRef` från SAML-begäran. | urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport |
| {SAML:NameIdPolicyFormat} | Attributet, `Format` från `NameIDPolicy` elementet i SAML-begäran. | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML:Utfärdare} |  SAML-elementvärdet `Issuer` för SAML-begäran.| `https://contoso.com` |
| {SAML:AllowCreate} | Attributvärdet, `AllowCreate` från `NameIDPolicy` elementet i SAML-begäran. | True |
| {SAML:ForceAuthn} | Attributvärdet, `ForceAuthN` från `AuthnRequest` elementet i SAML-begäran. | True |
| {SAML:ProviderName} | Attributvärdet, `ProviderName` från `AuthnRequest` elementet i SAML-begäran.| Contoso.com |

## <a name="using-claim-resolvers"></a>Använda anspråkslösare

Du kan använda anspråkslösare med följande element:

| Objekt | Element | Inställningar |
| ----- | ----------------------- | --------|
|Teknisk profil för Application Insights |`InputClaim` | |
|Teknisk profil för [Azure Active Directory](active-directory-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Teknisk profil för [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Teknisk profil för [OpenID Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Teknisk profil [för skadeomvandling](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[RESTful leverantör](restful-technical-profile.md) teknisk profil| `InputClaim`| 1, 2|
|[SAML2](saml-technical-profile.md) teknisk profil| `OutputClaim`| 1, 2|
|[Självförsäkrad](self-asserted-technical-profile.md) teknisk profil| `InputClaim`, `OutputClaim`| 1, 2|
|[InnehållDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile) teknisk profil| `OutputClaim`| 2 |

Inställningar:
1. Metadata `IncludeClaimResolvingInClaimsHandling` måste anges `true`till .
1. Attributet `AlwaysUseDefaultValue` input- eller utdataanspråk måste anges till `true`.

## <a name="claim-resolvers-samples"></a>Exempel på anspråkslösare

### <a name="restful-technical-profile"></a>RESTful teknisk profil

I en [RESTful](restful-technical-profile.md) teknisk profil kanske du vill skicka användarspråk, principnamn, omfattning och klient-ID. Baserat på anspråk kan REST API köra anpassad affärslogik och vid behov skapa ett lokaliserat felmeddelande.

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
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Direkt inloggning

Med hjälp av anspråkslösare kan du fylla i inloggningsnamnet eller direktinstallera till en viss leverantör av social identitet, till exempel Facebook, LinkedIn eller ett Microsoft-konto. Mer information finns [i Konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Anpassning av dynamiskt användargränssnitt

Med Azure AD B2C kan du skicka frågesträngparametrar till slutpunkterna för HTML-innehållsdefinition för att dynamiskt återge sidinnehållet. Med den här funktionen kan du till exempel ändra bakgrundsavbildningen på registrerings- eller inloggningssidan för Azure AD B2C baserat på en anpassad parameter som du skickar från ditt webb- eller mobilappprogram. Mer information finns i [Dynamiskt konfigurera användargränssnittet med hjälp av anpassade principer i Azure Active Directory B2C](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri). Du kan också lokalisera HTML-sidan baserat på en språkparameter eller ändra innehållet baserat på klient-ID.

Följande exempel skickas i frågesträngparametern **CampaignId** `Hawaii`med värdet `en-US`, en **språkkod** för och en **app** som representerar klient-ID:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Därför skickar Azure AD B2C ovanstående parametrar till HTML-innehållssidan:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definition av innehåll

I en [ContentDefinition](contentdefinitions.md) `LoadUri`kan du skicka anspråkslösare för att hämta innehåll från olika platser, baserat på de parametrar som används.

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Teknisk profil för Application Insights

Med Azure Application Insights och anspråkslösare kan du få insikter om användarbeteende. I den tekniska profilen Application Insights skickar du indataanspråk som sparas till Azure Application Insights. Mer information finns [i Spåra användarbeteende i Azure AD B2C-färder med hjälp av Application Insights](analytics-with-application-insights.md). I följande exempel skickas princip-ID, korrelations-ID, språk och klient-ID till Azure Application Insights.

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

### <a name="relying-party-policy"></a>Förlitande partipolitik

I en teknisk profil för [förlitande part](relyingparty.md) princip kan du skicka klient-ID eller korrelations-ID till det förlitande part-programmet inom JWT.

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
