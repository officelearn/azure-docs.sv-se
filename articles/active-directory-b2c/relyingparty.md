---
title: RelyingParty - Azure Active Directory B2C | Microsoft Docs
description: Ange det RelyingParty elementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5d42568a738d946d7df65601044b9797a35f6b1f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176020"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den **RelyingParty** elementet anger användarresa att upprätthålla för den aktuella begäran till Azure Active Directory (Azure AD) B2C. Den anger listan över anspråk som förlitande part (RP)-programmet måste också som en del av Utfärdad token. Ett RP-program, till exempel ett webb-, Mobil- eller skrivbordsprogram program, anropar principfilen som RP. Principfilen som RP utför en viss uppgift, till exempel inloggning, återställa ett lösenord eller redigering av profil. Fler program kan använda samma RP-princip och ett program kan använda flera principer. Alla RP-program får samma token med anspråk och användaren går igenom samma användarresa.

I följande exempel visas en **RelyingParty** elementet i den *B2C_1A_signup_signin* principfil:

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
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
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

Den valfria **RelyingParty** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Standardanvändarresa för RP-programmet. |
| UserJourneyBehaviors | 0:1 | Omfattning resa användarbeteenden. |
| TechnicalProfile | 1:1 | En tekniska profilen som stöds av RP-programmet. Den tekniska profilen innehåller ett kontrakt för RP-programmet att kontakta Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

Den `DefaultUserJourney` elementet anger en referens till identifieraren för användarresa som vanligtvis definieras i principen Base eller tillägg. I följande exempel visas den registrering eller inloggning användarresa som anges i den **RelyingParty** element:

*B2C_1A_signup_signin* princip:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* eller *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Den **DefaultUserJourney** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Tjänsten | Ja | En identifierare för användarresa i principen. Mer information finns i [användaren resor](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Den **UserJourneyBehaviors** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Omfånget för enkel inloggning (SSO) session beteendet för en användarresa. |
| SessionExpiryType |0:1 | Autentiseringsinställningar för sessionen. Möjliga värden: `Rolling` eller `Absolute`. Den `Rolling` värdet (standard) anger att användaren förblir inloggad så länge användaren är aktiv hela tiden i programmet. Den `Absolute` värdet anger att om användaren tvingas att autentiseras på nytt efter hur lång tid som anges av programmet session livslängd. |
| SessionExpiryInSeconds | 0:1 | Azure AD B2C sessions-cookie som angetts som ett heltal livstid lagras på användarens webbläsare efter lyckad autentisering. |
| JourneyInsights | 0:1 | Azure Application Insights-instrumenteringsnyckeln som ska användas. |
| ContentDefinitionParameters | 0:1 | Lista över nyckelvärdepar som ska läggas till innehållsdefinition belastningen URI. |

### <a name="singlesignon"></a>SingleSignOn

Den **SingleSignOn** elementet innehåller i följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Scope | Ja | Omfattning beteendet för enkel inloggning. Möjliga värden: `Suppressed`, `Tenant`, `Application`, eller `Policy`. Den `Suppressed` värdet anger att beteendet undertrycks. Till exempel när det gäller en enda session inloggnings-ingen session bibehålls för användaren och användaren uppge alltid ett val för identitets-provider. Den `TrustFramework` värdet anger att funktionen används för alla principer inom ramen för förtroende. Exempelvis kan en användare navigera genom två princip utbildning för ett förtroende-ramverk inte uppmanas att ange ett val för identitets-provider. Den `Tenant` värdet anger att funktionen används för alla principer i klienten. Exempelvis kan en användare navigera genom två princip utbildning för en klient inte uppmanas att ange ett val för identitets-provider. Den `Application` värdet anger att funktionen används för alla principer för program som gör begäran. En användare navigera genom två princip utbildning för ett program är till exempel inte ange ett val för identitets-provider. Den `Policy` värdet anger att problemet endast gäller för en princip. Till exempel en användare navigera genom två princip utbildning för ett förtroende-ramverk uppmanas att ange ett identitet providern val när du växlar mellan principer. |
| KeepAliveInDays | Ja | styr hur länge användaren förblir inloggad. Ange värdet till 0 inaktiveras KMSI funktioner. Mer information finns i [vill förbli inloggad](active-directory-b2c-reference-kmsi-custom.md). |

## <a name="journeyinsights"></a>JourneyInsights

Den **JourneyInsights** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| TelemetryEngine | Ja | Värdet måste vara `ApplicationInsights`. | 
| InstrumentationKey | Ja | Den sträng som innehåller instrumenteringsnyckeln för application insights-elementet. |
| DeveloperMode | Ja | Möjliga värden: `true` eller `false`. Om `true`, Application Insights underlättar telemetrin genom pipelinen för bearbetning. Den här inställningen är bra för utveckling, men begränsad vid stora volymer som är utformade för detaljerad aktivitetsloggar endast för att underlätta utvecklingen av anpassade principer. Använd inte utvecklingsläge i produktion. Loggar samla in alla anspråk som skickas till och från identitetsleverantör man under utvecklingen. Om används i produktion, ansvara utvecklaren för personligt identifierbar information (privat identifierbar Information) som samlas in i loggen för App Insights som de äger. De här detaljerade loggar samlas endast när det här värdet anges till `true`.|
| ClientEnabled | Ja | Möjliga värden: `true` eller `false`. Om `true`, skickar klientskript Application Insights för att spåra sidfel vy och klientsidan. | 
| ServerEnabled | Ja | Möjliga värden: `true` eller `false`. Om `true`, skickar den befintliga UserJourneyRecorder JSON som en anpassad händelse till Application Insights. | 
| TelemetryVersion | Ja | Värdet måste vara `1.0.0`. | 

Mer information finns i [samla in loggar](active-directory-b2c-troubleshoot-custom.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Genom att använda anpassade principer i Azure AD B2C kan skicka du en parameter i en frågesträng. Genom att skicka parametern till HTML-slutpunkten kan du dynamiskt ändra sidinnehållet. Du kan till exempel ändra bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram. Azure AD B2C skickar frågan strängparametrar till dynamisk HTML-filen, till exempel aspx-fil. 

I följande exempel skickas en parameter med namnet `campaignId` med värdet `hawaii` i frågesträngen:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Den **ContentDefinitionParameters** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | En sträng som innehåller viktiga värde-par som läggs till i frågesträngen för en innehållsdefinition belastning URI. |

Den **ContentDefinitionParameter** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Name | Ja | Namnet på nyckeln värde-par. |

Mer information finns i [konfigurera Användargränssnittet med dynamiskt innehåll med hjälp av anpassade principer](active-directory-b2c-ui-customization-custom-dynamic.md)

## <a name="technicalprofile"></a>TechnicalProfile

Den **TechnicalProfile** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- | 
| Id | Ja | Värdet måste vara `PolicyProfile`. |

Den **TechnicalProfile** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Den sträng som innehåller namnet på den tekniska profilen som visas för användarna. |
| Beskrivning | 0:1 | Den sträng som innehåller en beskrivning av den tekniska profilen som visas för användarna. |
| Protokoll | 1:1 | Det protokoll som används för federationen. |
| Metadata | 0:1 | Insamling av *objekt* av nyckel/värde-par som används av protokollet för att kommunicera med slutpunkten under en transaktion konfigurera interaktion mellan den förlitande parten och andra community-deltagare. |
| OutputClaims | 0:1 | En lista över anspråkstyper som vidtas som utdata i den tekniska profilen. Var och en av de här elementen refereras till av en **ClaimType** redan har definierats i den **ClaimsSchema** avsnittet eller i en princip som den här principfil ärver. |
| SubjectNamingInfo | 0:1 | Ämnesnamn som används i token. |

Den **protokollet** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Name | Ja | Namnet på ett giltigt protokoll som stöds av Azure AD B2C som används som en del av den tekniska profilen. Möjliga värden: `OpenIdConnect` eller `SAML2`. Den `OpenIdConnect` värdet representerar OpenID Connect 1.0-protokollstandard enligt OpenID foundation-specifikationen. Den `SAML2` representerar SAML 2.0-protokollstandard enligt OASIS-specifikationen. Använd inte en SAML-token i produktion. |

## <a name="outputclaims"></a>OutputClaims

Den **OutputClaims** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | Namnet på en förväntade Anspråkstyp i listan stöds för principen som den förlitande parten prenumererar. Det här anspråket fungerar som utdata för den tekniska profilen. |

Den **OutputClaim** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | En referens till en **ClaimType** redan har definierats i den **ClaimsSchema** -avsnittet i principen. |
| Standardvärde | Nej | Ett standardvärde som kan användas om anspråksvärdet är tom. |
| PartnerClaimType | Nej | Skickar anspråket i ett annat namn som konfigurerats i ClaimType-definition. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Med den **SubjectNameingInfo** element du kontrollerar värdet för ämnet token:
- **JTW token** – `sub` anspråk. Det här är ett huvudnamn för som token kontrollerar information, t.ex användare av ett program. Det här värdet kan inte ändras och det går inte att tilldela om eller återanvänds. Det kan användas för att utföra auktoriseringskontroller för säker, till exempel när token används för att komma åt en resurs. Som standard fylls anspråk för ämne med objekt-ID för användaren i katalogen. Mer information finns i [Token, session och konfiguration för enkel inloggning](active-directory-b2c-token-session-sso.md).
- **SAML-token** – `<Subject><NameID>` element som identifierar elementet ämne.

Den **SubjectNamingInfo** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClaimType | Ja | En referens till en utdata-anspråket **PartnerClaimType**. Utdata anspråk måste definieras i principen för förlitande part **OutputClaims** samling. |

I följande exempel visas hur du definierar en OpenId Connect förlitande part. Information för ämne namn har konfigurerats som den `objectId`:

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
Detta JWT-token innehåller den `sub` anspråk med objekt-ID för användaren:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


