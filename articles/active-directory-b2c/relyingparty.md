---
title: RelyingParty - Azure Active Directory B2C | Microsoft-dokument
description: Ange elementet RelyingParty i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 733a33881fe3acc962aeda4b05a1b01be4e148ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680367"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Elementet RelyingParty** anger användarfärden för att framtvinga för den aktuella begäran till Azure Active Directory B2C (Azure AD B2C). Den anger också en lista över anspråk som det förlitande parten (RP) programmet behöver som en del av den utfärdade token. Ett RP-program, till exempel ett webb-, mobil- eller skrivbordsprogram, anropar RP-principfilen. RP-principfilen kör en viss uppgift, till exempel logga in, återställa ett lösenord eller redigera en profil. Flera program kan använda samma RP-princip och ett enda program kan använda flera principer. Alla RP-program får samma token med anspråk och användaren går igenom samma användarresa.

I följande exempel visas ett **RelyingParty-element** i *B2C_1A_signup_signin* principfilen:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Det valfria **RelyingParty-elementet** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Standardanvändarfärden för RP-programmet. |
| AnvändareJourneyBehaviors | 0:1 | Omfattningen av användarens färdbeteenden. |
| Tekniskprofil | 1:1 | En teknisk profil som stöds av RP-programmet. Den tekniska profilen tillhandahåller ett kontrakt för RP-programmet för att kontakta Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

Elementet `DefaultUserJourney` anger en referens till identifieraren för användarens färd som vanligtvis definieras i principen Bas eller Tillägg. I följande exempel visas den registrerings- eller inloggningsanvändarresa som anges i elementet **RelyingParty:**

*B2C_1A_signup_signin* politik:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* eller *B2C_1A_TrustFrameworkExtensionPolicy:*

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

**Elementet DefaultUserJourney** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för användarens färd i principen. Mer information finns i [användarresor](userjourneys.md) |

## <a name="userjourneybehaviors"></a>AnvändareJourneyBehaviors

Elementet **UserJourneyBehaviors** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Omfattningen av den enda inloggningssessionen (SSO) sessionsbeteendet för en användarresa. |
| SessionExpiryType |0:1 | Autentiseringsbeteendet för sessionen. Möjliga `Rolling` värden: `Absolute`eller . Värdet `Rolling` (standard) anger att användaren förblir inloggad så länge användaren är kontinuerligt aktiv i programmet. Värdet `Absolute` anger att användaren tvingas att omauktisera efter den tidsperiod som anges av programsessionens livstid. |
| SessionExpiryInSeconds | 0:1 | Livslängden för Azure AD B2C:s sessionscookie som anges som ett heltal som lagras i användarens webbläsare vid lyckad autentisering. |
| ResorInsikter | 0:1 | Instrumenteringsnyckeln Azure Application Insights som ska användas. |
| ContentDefinitionParameters | 0:1 | Listan över nyckelvärdepar som ska läggas till i innehållsdefinitionen läser in URI. |
|ScriptExecution| 0:1| De [javascript-körningslägen som](javascript-samples.md) stöds. Möjliga `Allow` värden: `Disallow` eller (standard).

### <a name="singlesignon"></a>SingleSignOn

**SingleSignOn-elementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Omfång | Ja | Omfattningen av det enskilda inloggningsbeteendet. Möjliga `Suppressed`värden: `Tenant` `Application`, `Policy`, eller . Värdet `Suppressed` anger att beteendet undertrycks och att användaren alltid uppmanas att välja en identitetsprovider.  Värdet `Tenant` anger att beteendet tillämpas på alla principer i klienten. En användare som navigerar genom två principresor för en klient uppmanas till exempel inte för ett identitetsproviderval. Värdet `Application` anger att beteendet tillämpas på alla principer för programmet som gör begäran. En användare som navigerar genom två principresor för ett program uppmanas till exempel inte för ett identitetsproviderval. Värdet `Policy` anger att beteendet endast gäller för en princip. En användare som navigerar genom två principresor för ett förtroenderamverk uppmanas till exempel för ett identitetsproviderval när du växlar mellan principer. |
| KeepAliveInDays | Ja | Styr hur länge användaren förblir inloggad. Om du ställer in värdet på 0 inaktiveras KMSI-funktionen. Mer information finns [i Håll mig inloggad](custom-policy-keep-me-signed-in.md). |
|EnforceIdTokenHintOnLogout| Inga|  Tvinga fram en tidigare utfärdad ID-token till utloggningsslutpunkten som ett tips om slutanvändarens aktuella autentiserade session med klienten. Möjliga värden: `false` (standard) eller `true`. Mer information finns [i Webb-inloggning med OpenID Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>ResorInsikter

Elementet **JourneyInsights** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| TelemetriEngine | Ja | Värdet måste `ApplicationInsights`vara . |
| Instrumenteringsnyckel | Ja | Strängen som innehåller instrumenteringsnyckeln för elementet application insights. |
| UtvecklareMode | Ja | Möjliga `true` värden: `false`eller . Om `true`påskyndar Application Insights telemetrin via bearbetningspipelinen. Den här inställningen är bra för utveckling, men begränsad till höga volymer De detaljerade aktivitetsloggarna är endast utformade för att underlätta utvecklingen av anpassade principer. Använd inte utvecklingsläge i produktionen. Loggar samlar in alla anspråk som skickas till och från identitetsleverantörerna under utvecklingen. Om den används i produktionen tar utvecklaren på sig ansvaret för PII (Privat identifierbar information) som samlas in i appinsiktsloggen som de äger. Dessa detaljerade loggar samlas bara in när `true`det här värdet är inställt på .|
| ClientEnabled | Ja | Möjliga `true` värden: `false`eller . Om `true`, skickar application insights-skriptet på klientsidan för spårning av sidvisning och fel på klientsidan. |
| ServerEnabled | Ja | Möjliga `true` värden: `false`eller . Om `true`, skickar den befintliga UserJourneyRecorder JSON som en anpassad händelse till Application Insights. |
| TelemetriVersion | Ja | Värdet måste `1.0.0`vara . |

Mer information finns i [Samla in loggar](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Genom att använda anpassade principer i Azure AD B2C kan du skicka en parameter i en frågesträng. Genom att skicka parametern till HTML-slutpunkten kan du dynamiskt ändra sidinnehållet. Du kan till exempel ändra bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram. Azure AD B2C skickar frågesträngparametrarna till din dynamiska HTML-fil, till exempel px-filen.

I följande exempel skickas `campaignId` en parameter `hawaii` med namnet med värdet i frågesträngen:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

**ContentDefinitionParameters-elementet** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| InnehållDefinitionParameter | 0:n | En sträng som innehåller nyckelvärdeparet som läggs till i frågesträngen för en innehållsdefinition läser in URI. |

**ContentDefinitionParameter-elementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Namn | Ja | Namnet på nyckelvärdeparet. |

Mer information finns i [Konfigurera användargränssnittet med dynamiskt innehåll med hjälp av anpassade principer](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>Tekniskprofil

Elementet **TechnicalProfile** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | Värdet måste `PolicyProfile`vara . |

**TechnicalProfile** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Strängen som innehåller namnet på den tekniska profilen. |
| Beskrivning | 0:1 | Strängen som innehåller beskrivningen av den tekniska profilen. |
| Protokoll | 1:1 | Protokollet som används för federationen. |
| Metadata | 0:1 | Samlingen av *objekt* av nyckel/värde-par som används av protokollet för att kommunicera med slutpunkten under en transaktion för att konfigurera interaktion mellan den förlitande parten och andra deltagare i communityn. |
| OutputClaims | 1:1 | En lista över anspråkstyper som tas ut som utdata i den tekniska profilen. Vart och ett av dessa element innehåller en referens till en **ClaimType som** redan har definierats i avsnittet **ClaimsSchema** eller i en princip som den här principfilen ärver från. |
| ÄmneNamingInfo | 1:1 | Ämnesnamnet som används i token. |

**Protokollelementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Namn | Ja | Namnet på ett giltigt protokoll som stöds av Azure AD B2C och som används som en del av den tekniska profilen. Möjliga `OpenIdConnect` värden: `SAML2`eller . Värdet `OpenIdConnect` representerar OpenID Connect 1.0-protokollstandarden enligt OpenID foundation-specifikationen. Den `SAML2` representerar SAML 2.0-protokollstandarden enligt OASIS-specifikationen. |

## <a name="outputclaims"></a>OutputClaims

**Elementet OutputClaims** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Namnet på en förväntad anspråkstyp i listan över stöds för den princip som den förlitande parten prenumererar på. Detta påstående fungerar som en utdata för den tekniska profilen. |

**Elementet OutputClaim** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | En referens till en **ClaimType som** redan definierats i avsnittet **ClaimsSchema** i principfilen. |
| Standardvärde | Inga | Ett standardvärde som kan användas om anspråksvärdet är tomt. |
| PartnerClaimType | Inga | Skickar anspråket i ett annat namn som konfigurerats i ClaimType-definitionen. |

### <a name="subjectnaminginfo"></a>ÄmneNamingInfo

Med elementet **SubjectNameingInfo** styr du värdet för tokenämnet:
- **JWT token** `sub` - anspråket. Detta är ett huvudnamn som token bekräftar information om, till exempel användaren av ett program. Det här värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Den kan användas för att utföra säkra auktoriseringskontroller, till exempel när token används för att komma åt en resurs. Som standard fylls ämnesanspråket med användarens objekt-ID i katalogen. Mer information finns i [Token, session och enkel inloggningskonfiguration](session-behavior.md).
- **SAML-token** `<Subject><NameID>` - det element som identifierar ämneselementet.

**Elementet SubjectNamingInfo** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimType (Påståttstyp) | Ja | En referens till ett utdataanspråks **PartnerClaimType**. Utdataanspråken måste definieras i den förlitande partens policy **OutputClaims-samling.** |

I följande exempel visas hur du definierar en openid connect-förlitande part. Ämnesnamnsinformationen `objectId`är konfigurerad som:

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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
JWT-token innehåller `sub` anspråket med användaren objectId:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
