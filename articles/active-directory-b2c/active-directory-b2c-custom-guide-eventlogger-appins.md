---
title: Spåra användarnas beteende med hjälp av händelser i Application Insights från Azure Active Directory B2C | Microsoft Docs
description: Stegvis guide för att aktivera händelseloggar i Application Insights från Azure AD B2C användaren utbildning genom att använda anpassade principer (förhandsversion)
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c77feed3b86358c74f741b53aa03ecb454dc9a62
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337110"
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Spåra användarnas beteende i Azure AD B2C-transporter med hjälp av Application Insights

Azure Active Directory B2C (Azure AD B2C) fungerar bra med Azure Application Insights. De ger detaljerad och anpassade händelseloggar för dina egna anpassade användaren resor. Den här artikeln visar hur du kommer igång så att du kan:

* Få insikter om användarbeteende.
* Felsöka dina egna principer i utveckling eller produktion.
* Mäta prestanda.
* Skapa aviseringar från Application Insights.

> [!NOTE]
> Den här funktionen är en förhandsversion.

## <a name="how-it-works"></a>Hur det fungerar

Identitetsramverk i Azure AD B2C innehåller nu providern `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Händelsedata skickas direkt till Application Insights med hjälp av instrumentationsnyckeln tillhandahålls till Azure AD B2C.

Tekniska profilen använder den här providern för att definiera en händelse från B2C.  Profilen anger namnet på händelsen, de anspråk som kommer att läggas till och instrumenteringsnyckeln.  Om du vill publicera en händelse läggs sedan den tekniska profilen som en `orchestration step` eller som en `validation technical profile` i en anpassad användarresa.

Application Insights kan en enhetlig händelser med ett Korrelations-ID för att registrera en användarsession. Application Insights gör den händelsen och sessionen tillgänglig inom några sekunder och visar många visualisering, export och analysverktyg.

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md). Den här artikeln förutsätter att du använder startpaket för anpassad princip. Men startpaket krävs inte.

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Steg 1. Skapa en Application Insights-resurs och få instrumenteringsnyckeln

När du använder Application Insights med Azure AD B2C, är det enda kravet att skapa en resurs och få en instrumenteringsnyckel. Du skapar en resurs i den [Azure-portalen.](https://portal.azure.com)

1. I Azure-portalen inom din prenumeration-klient väljer **+ skapa en resurs**. Den här klienten är inte din Azure AD B2C-klient.  
2. Sök efter och välj **Application Insights**.  
3. Skapa en resurs som använder **ASP.NET-webbprogram** som **programtyp**, under en prenumeration på dina inställningar.
4. När du har skapat Application Insights-resursen, öppna den och Observera instrumenteringsnyckeln.

![Översikt över Application Insights och Instrumenteringsnyckeln](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Steg 2. Lägga till nya ClaimType definitioner i filen förtroende framework-tillägg

Öppna tilläggsfilen från startpaket och Lägg till följande element i den `<BuildingBlocks>` noden. Filnamnet är vanligtvis `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>Steg 3. Lägga till nya tekniska profiler som använder Application Insights-providern

Tekniska profiler kan ses funktioner i den identitet upplevelse Framework av Azure AD B2C. Det här exemplet definierar fem tekniska profiler för att öppna en session och publicerar händelser:

| Tekniska profilen | Aktivitet |
| ----------------- | -----|
| AzureInsights-Common | Skapar en gemensam uppsättning parametrar som ska ingå i alla AzureInsights tekniska profiler | 
| JourneyContextForInsights | Öppnas sessionen i Application Insights och skickar ett Korrelations-ID |
| AzureInsights-SignInRequest | Skapar en `SignIn` händelse med en uppsättning anspråk när en inloggningsbegäran har tagits emot | 
| AzureInsights-UserSignup | Skapar en UserSignup händelse när användaren utlöser inloggningsalternativet i en registrerings-registreringen/inloggning resa | 
| AzureInsights-SignInComplete | Registrerar en autentisering slutförs när en token har skickats till den förlitande partsprogram | 

Lägg till profilerna i tilläggsfilen från startpaket genom att lägga till dessa element och den `<ClaimsProviders>` noden.  Filnamnet är vanligtvis `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Ändra instrumenteringsnyckeln i den `ApplicationInsights-Common` tekniska profil till det GUID som innehåller Application Insights-resursen.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="JourneyContextForInsights">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
      </OutputClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Steg 4. Lägga till de tekniska profilerna för Application Insights enligt orchestration-steg i en befintlig användarresa

Anropa `JournyeContextForInsights` som orchestration-steg 1:

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Anropa `Azure-Insights-SignInRequest` som orchestration steg 2 för att spåra en inloggning-i/registrering begäran har tagits emot:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Omedelbart *innan* den `SendClaims` orkestrering kan du lägga till ett nytt steg som anropar `Azure-Insights-UserSignup`. Utlöses när användaren väljer knappen registrera dig i en registrerings-registreringen/inloggning resa.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="9" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
```

Direkt efter den `SendClaims` orkestreringssteget, anrop `Azure-Insights-SignInComplete`. Det här steget visar en resa som har slutförts.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> När du har lagt till nya orchestration-steg kan du numrera om stegen sekventiellt utan hoppar över alla heltal från 1 till N.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Steg 5. Ladda upp tilläggsfilen ändrade, kör principen och visa händelser i Application Insights

Spara och ladda upp den nya förtroende framework-tillägg-filen. Anropa sedan principen för förlitande part från ditt program eller Använd `Run Now` i Azure AD B2C-gränssnittet. Händelser är tillgängliga i Application Insights i sekunder.

1. Öppna den **Application Insights** resurs i Azure Active Directory-klient.
2. Välj **användning** > **händelser**.
3. Ange **under** till **senaste timmen** och **av** till **3 minuter**.  Du kan behöva välja **uppdatera** att visa resultat.

![Application Insights-Användningshändelser Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>Nästa steg

Lägg till anspråkstyper och händelser användarresan som passar dina behov. Här är en lista över möjliga anspråk, med hjälp av ytterligare anspråk matchare

### <a name="culture-specific-claims"></a>Kultur-specifika anspråk

```xml
Referenced using: {Culture:One of the property names below}
```

| Begär | Definition | Exempel |
| ----- | -----------| --------|
| LanguageName | De två enhetsbokstaven ISO-kod för språk | en |
| RegionName | De två enhetsbokstaven ISO-kod för regionen | USA |
| RFC5646 | RFC5646 språkkod | sv-SE |
| LCID   | LCID för språkkod | 29 |

### <a name="policy-specific-claims"></a>Princip-specifika anspråk

```xml
Referenced using {Policy:One of the property names below}
```

| Begär | Definition | Exempel |
| ----- | -----------| --------|
| TrustFrameworkTenantId | Trustframework-klient-id | Gäller inte |
| RelyingPartyTenantId | Klient-id för den förlitande parten | Gäller inte |
| PolicyId | Princip-id för principen | Gäller inte |
| TenantObjectId | Klient objekt-id för principen | Gäller inte |

### <a name="openid-connect-specific-claims"></a>OpenID Connect-specifika anspråk

```xml
Referenced using {OIDC:One of the property names below}
```

| Begär | OpenIdConnect parameter | Exempel |
| ----- | ----------------------- | --------|
| fråga | fråga | Gäller inte |
| LoginHint |  login_hint | Gäller inte |
| DomainHint | domain_hint | Gäller inte |
|  MaxAge | max_age | Gäller inte |
| ClientId | client_id | Gäller inte |
| Användarnamn | login_hint | Gäller inte |
|  Resurs | resurs| Gäller inte |
| AuthenticationContextReferences | acr_values | Gäller inte |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Icke-protokollparametrar ingår OIDC & OAuth2 begäranden

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Alla parameternamn som en del av ett OIDC eller OAuth2-begäran kan mappas till ett anspråk i användarresan. Du kan sedan registrera det i händelsen. Till exempel begärande från programmet kan innehålla en frågesträngsparameter med namnet `app_session`, `loyalty_number` eller `any_string`.

Här är ett exempel på begäran från programmet:

```
https://sampletenant.b2clogin.com/tfp/sampletenant.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Du kan sedan lägga till anspråk genom att lägga till en `Input Claim` element på Application Insights-händelsen. Egenskaperna för en händelse läggs till via syntax {egenskapen: NAME}, där NAME egenskapen läggs till händelsen. Exempel:

```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Andra system-anspråk

Vissa system anspråk måste läggas till i uppsättningen anspråk innan de kan spela in som händelser. Den tekniska profilen `SimpleUJContext` måste anropas som en orchestration-steg eller en profil för tekniska innan dessa anspråk är tillgängliga.

```xml
<ClaimsProvider>
  <DisplayName>User Journey Context Provider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SimpleUJContext">
      <DisplayName>User Journey Context Provide</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="IP-Address" />
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
        <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
        <OutputClaim ClaimTypeReferenceId="Build" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```


