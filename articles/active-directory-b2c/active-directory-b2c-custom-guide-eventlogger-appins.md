---
title: Spåra användarens beteende med hjälp av händelser i Application Insights från Azure Active Directory B2C | Microsoft Docs
description: Stegvisa instruktioner för att aktivera händelseloggar i Application Insights från Azure AD B2C användaren transporter med hjälp av anpassade principer (förhandsgranskning)
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 04/16/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 94d96af8db651a848ac092d1f8b85da4909427b7
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110123"
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Spåra användarens beteende i Azure AD B2C resor med hjälp av Application Insights

Azure Active Directory B2C (Azure AD B2C) fungerar bra med Azure Application Insights. De ger detaljerad och anpassade händelseloggar för din anpassade användaren resor. Den här artikeln visar hur du kommer igång så att du kan:

* Få insikter om beteendet för användaren.
* Felsöka dina egna principer under utveckling eller i produktion.
* Mäter prestanda.
* Skapa aviseringar från Application Insights.

> [!NOTE]
> Den här funktionen är en förhandsversion.

## <a name="how-it-works"></a>Hur det fungerar

Identitet upplevelse ramverk i Azure AD B2C innehåller nu provider `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Händelsedata skickas direkt till Application Insights med instrumentation nyckel till Azure AD B2C.

En profil för tekniska använder den här providern för att definiera en händelse från B2C.  Profilen anger namnet på händelsen, vilka anspråk som kommer att spelas in och instrumentation nyckel.  Om du vill skicka en händelse tekniska profilen sedan läggs till som en `orchestration step` eller som en `validation technical profile` i en anpassad användare resa.

Application Insights förenar händelser med hjälp av en Korrelations-ID för att registrera en användarsession. Application Insights gör händelse och session tillgänglig inom några sekunder och presenteras många visualiseringen export och analysverktyg.

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md). Den här artikeln förutsätter att du använder startpaket anpassad princip. Men startpaket krävs inte.

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Steg 1. Skapa en Application Insights-resurs och hämta nyckel för instrumentation

När du använder Application Insights med Azure AD B2C, är det enda kravet att skapa en resurs och få en instrumentation nyckel. Du skapar en resurs i den [Azure-portalen.](https://portal.azure.com)

1. Välj i Azure-portalen i din prenumeration klient **+ skapa en resurs för**. Den här klienten är inte din Azure AD B2C-klient.  
2. Söka efter och välja **Programinsikter**.  
3. Skapa en resurs som använder **ASP.NET-webbprogram** som **programtyp**, under en prenumeration på dina inställningar.
4. När du har skapat Application Insights-resurs, öppna det och anteckna nyckeln instrumentation.

![Översikt över Application Insights och Instrumentation nyckel](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Steg 2. Lägga till nya ClaimType definitioner i filen förtroende framework tillägg

Öppna tilläggsfilen från startpaket och Lägg till följande element till den `<BuildingBlocks>` nod. Filnamnet är vanligtvis `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

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

Tekniska profiler kan ses funktioner i den identitet upplevelse Framework av Azure AD B2C. Det här exemplet definierar fem tekniska profiler för att öppna en session och efter händelser:

| Tekniska profil | Aktivitet |
| ----------------- | -----|
| AzureInsights-Common | Skapar en gemensam uppsättning parametrar som ska inkluderas i alla AzureInsights tekniska profiler | 
| JourneyContextForInsights | Öppnar sessionen i Application Insights och skickar en Korrelations-ID |
| AzureInsights-SignInRequest | Skapar en `SignIn` händelse med en uppsättning anspråk när en begäran om inloggning har tagits emot | 
| AzureInsights-UserSignup | Skapar en UserSignup händelse när användaren utlöser registreringsalternativ i en sign-upp/inloggning resa | 
| AzureInsights-SignInComplete | Registrerar slutförande av en autentisering när en token har skickats till förlitande partsprogram | 

Lägg till profilerna i tilläggsfilen från startpaket genom att lägga till dessa element och den `<ClaimsProviders>` nod.  Filnamnet är vanligtvis `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Ändra nyckeln instrumentation i den `ApplicationInsights-Common` tekniska profil till GUID som tillhandahåller Application Insights-resurs.

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

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Steg 4. Lägg till tekniska profiler för Application Insights som orchestration-steg i en befintlig användare resa

Anropa `JournyeContextForInsights` som orchestration-steg 1:

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Anropa `Azure-Insights-SignInRequest` som orchestration steg 2 för att spåra en logga-i/sign-upp begäran har tagits emot:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Omedelbart *innan* den `SendClaims` orchestration kan du lägga till ett nytt steg som anropar `Azure-Insights-UserSignup`. Utlöses när användaren väljer knappen registreringen på en resa sign-upp/inloggning.

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

Omedelbart efter den `SendClaims` orchestration-steg, anropet `Azure-Insights-SignInComplete`. Det här steget visar en har slutförts resa.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> När du lägger till nya orchestration-steg kan du numrera om stegen sekventiellt utan hoppar över alla heltal från 1 till N.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Steg 5. Överför tilläggsfilen ändrade, kör principen och visa händelser i Application Insights

Spara och ladda upp nya förtroende framework tilläggsfilen. Anropa sedan principen för förlitande part från ditt program eller Använd `Run Now` i Azure AD B2C-gränssnittet. I sekunder finns händelserna i Application Insights.

1. Öppna den **Programinsikter** resurs i din Azure Active Directory-klient.
2. Välj **användning** > **händelser**.
3. Ange **under** till **senaste timmen** och **av** till **3 minuter**.  Du kan behöva välja **uppdatera** att visa resultat.

![Application Insights-Användningshändelser Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>Nästa steg

Lägga till anspråkstyper och händelser i dina användare resa så att de passar dina behov. Här är en lista över möjliga anspråk, med ytterligare anspråk matchare

### <a name="culture-specific-claims"></a>Kultur-specifika anspråk

```xml
Referenced using: {Culture:One of the property names below}
```

| Begär | Definition | Exempel |
| ----- | -----------| --------|
| LanguageName | De två enhetsbokstaven ISO-koden för språket | en |
| RegionName | De två enhetsbokstaven ISO-koden för regionen | USA |
| RFC5646 | RFC5646 språkkod | sv-SE |
| LCID   | LCID för språkkod | 29 |

### <a name="policy-specific-claims"></a>Princip-specifika anspråk

```xml
Referenced using {Policy:One of the property names below}
```

| Begär | Definition | Exempel |
| ----- | -----------| --------|
| TrustFrameworkTenantId | Trustframework klient-id | Gäller inte |
| RelyingPartyTenantId | Klient-id för den förlitande parten | Gäller inte |
| PolicyId | Princip-id för principen | Gäller inte |
| TenantObjectId | Objekt-id för innehavaren av principen | Gäller inte |

### <a name="openid-connect-specific-claims"></a>OpenID Connect-specifika anspråk

```xml
Referenced using {OIDC:One of the property names below}
```

| Begär | OpenIdConnect parameter | Exempel |
| ----- | ----------------------- | --------|
| Fråga | kommandotolk | Gäller inte |
| LoginHint |  login_hint | Gäller inte |
| DomainHint | domain_hint | Gäller inte |
|  MaxAge | max_age | Gäller inte |
| clientId | client_id | Gäller inte |
| Användarnamn | login_hint | Gäller inte |
|  Resurs | resurs| Gäller inte |
| AuthenticationContextReferences | acr_values | Gäller inte |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Icke-protokollparametrar medföljer OIDC & OAuth2 begäranden

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Varje parameternamn som ingår i en OIDC eller OAuth2-begäran kan mappas till ett anspråk i användarens transporten. Du kan sedan registrera det i händelsen. Till exempel en begäran från programmet kan innehålla en frågesträngparametern med namnet `app_session`, `loyalty_number` eller `any_string`.

Här är ett exempel på begäran från programmet:

```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Du kan sedan lägga till anspråk genom att lägga till en `Input Claim` element till Application Insights-händelsen. Egenskaper för en händelse har lagts till via syntax {egenskapen: NAME}, där namnet egenskap läggs till händelsen. Exempel:

```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Andra system anspråk

Vissa system anspråk måste läggas till i uppsättningen anspråk innan de kan registrera som händelser. Tekniska profilen `SimpleUJContext` måste anropas som ett orchestration-steg eller en profil för validering av teknisk innan dessa anspråk är tillgängliga.

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


