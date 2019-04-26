---
title: Om anspråk matchare i anpassade principer för Azure Active Directory B2C | Microsoft Docs
description: Läs mer om hur anspråk matchare används i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 44ac4a5fd14d262fdbd1f6fcd36bb2351d08f754
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313842"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Om anspråk matchare i Azure Active Directory B2C anpassade principer

Gör anspråk på matchare i Azure Active Directory (Azure AD) B2C [anpassade principer](active-directory-b2c-overview-custom.md) ger kontextinformation om en begäran om godkännande, till exempel namnet på principen, frågekorrelations-ID, språk för användargränssnittet och mer.

Om du vill använda en matchare anspråk i ett inkommande eller utgående anspråk, definierar du en sträng **ClaimType**under den [ClaimsSchema](claimsschema.md) element, och sedan ange den **DefaultValue** på anspråket matchning i indata eller utdata anspråk element. Azure AD B2C läser värdet för anspråket matcharen och använder värdet i den tekniska profilen. 

I följande exempel visas en Anspråkstyp med namnet `correlationId` definieras med en **DataType** av `string`.  

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

Mappa matcharen anspråk i den tekniska profilen till Anspråkstypen. Azure AD B2C fylls värdet för anspråket matcharen `{Context:CorrelationId}` i anspråket `correlationId` och skickar anspråk till den tekniska profilen.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Matchare anspråkstyper

Följande avsnitt listar tillgängliga anspråk matchare.

### <a name="culture"></a>Kultur

| Begäran | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Kultur: LanguageName} | De två enhetsbokstaven ISO-kod för språket. | en |
| {Culture:LCID}   | LCID för språkkod. | 29 |
| {Culture:RegionName} | De två enhetsbokstaven ISO-kod för regionen. | USA |
| {Culture:RFC5646} | Språkkoden RFC5646. | en-US |

### <a name="policy"></a>Princip

| Begäran | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Policy:PolicyId} | Förlitande part principens namn. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | Klient-ID för förlitande part. | din tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | Det objekt-ID för innehavare för förlitande part. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | Klient-ID för ramen förtroende. | din tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Begäran | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |Den `acr_values` frågesträngparametern. | Gäller inte |
| {OIDC:ClientId} |Den `client_id` frågesträngparametern. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |Den `domain_hint` frågesträngparametern. | facebook.com |
| {OIDC:LoginHint} |  Den `login_hint` frågesträngparametern. | someone@contoso.com |
| {OIDC:MaxAge} | Den `max_age`. | Gäller inte |
| {OIDC:Nonce} |Den `Nonce` frågesträngparametern. | defaultNonce |
| {OIDC:Prompt} | Den `prompt` frågesträngparametern. | inloggning |
| {OIDC:Resource} |Den `resource` frågesträngparametern. | Gäller inte |
| {OIDC:scope} |Den `scope` frågesträngparametern. | openid |

### <a name="context"></a>Kontext

| Begäran | Beskrivning | Exempel |
| ----- | ----------- | --------|
| {Context:BuildNumber} | Identitetsramverk versionen (build-nummer).  | 1.0.507.0 |
| {Context:CorrelationId} | Korrelations-ID  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Datum-tid i UTC.  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |Distributionsläge för principen.  | Produktion |
| {Context:IPAddress} | Användaren IP-adressen. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Parametrar för icke-protokoll

Alla parameternamn som en del av ett OIDC eller OAuth2-begäran kan mappas till ett anspråk i användarresan. Till exempel begärande från programmet kan innehålla en frågesträngsparameter med namnet `app_session`, `loyalty_number`, eller alla anpassade frågesträngen.

| Begäran | Beskrivning | Exempel |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | En frågesträngsparameter. | hawaii |
| {OAUTH-KV:app_session} | En frågesträngsparameter. | A3C5R |
| {OAUTH-KV:loyalty_number} | En frågesträngsparameter. | 1234 |
| {OAUTH-KV: alla anpassade frågesträngen} | En frågesträngsparameter. | Gäller inte |

### <a name="oauth2"></a>OAuth2

| Begäran | Beskrivning | Exempel |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Åtkomst-token. | Gäller inte |

## <a name="how-to-use-claim-resolvers"></a>Hur du använder anspråk matchare

### <a name="restful-technical-profile"></a>RESTful-tekniska profilen

I en [RESTful](restful-technical-profile.md) tekniska profilen kan du skicka användarspråket, principnamn, omfång och klient-ID. Baserat på de här anspråken REST-API kan köra anpassad affärslogik och generera ett lokaliserat felmeddelande om det behövs. 

I följande exempel visas en RESTful tekniska profil:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Direkt inloggning

Använda anspråk matchare kan förkonfigurera du inloggningsnamn eller direkt inloggning till en viss social identitetsprovider, till exempel Facebook, LinkedIn eller ett Microsoft-konto. Mer information finns i [konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamisk anpassning av Användargränssnittet

Azure AD B2C kan du överföra parametrar för frågesträngen i HTML-innehållsdefinition slutpunkterna så att du kan rendera sidinnehållet dynamiskt. Du kan till exempel ändra bakgrundsbilden på Azure AD B2C-registrering eller inloggning sidan baserat på en anpassad parameter som du skickar från dina webb- och mobilprogram. Mer information finns i [dynamiskt konfigurera Användargränssnittet med hjälp av anpassade principer i Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md). Du kan också lokalisera HTML-sidan baserat på ett språk-parametern eller ändra innehållet baserat på klient-ID.

I följande exempel skickas i frågesträngen en parameter med namnet **campaignId** med värdet `hawaii`, ett **språk** koden för `en-US`, och **app** som representerar klient-ID:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Azure AD B2C skickar därför ovanstående parametrar till sidan HTML-innehåll:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Application Insights-tekniska profilen

Med Azure Application Insights och anspråk matchare kan du få insikter om användarbeteende. I den tekniska profilen i Application Insights skickar du inkommande anspråk som sparas till Azure Application Insights. Mer information finns i [spåra användarnas beteende i Azure AD B2C-transporter med hjälp av Application Insights](active-directory-b2c-custom-guide-eventlogger-appins.md). I följande exempel skickar princip-ID, Korrelations-ID, språk och klient-ID till Azure Application Insights.

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
