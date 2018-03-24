---
title: Hur att spåra användarens beteende med hjälp av händelser i Application Insights från Azure AD B2C | Microsoft Docs
description: Stegvisa instruktioner för att aktivera händelseloggar i Application Insights från Azure AD B2C användaren resor anpassade principer - FÖRHANDSGRANSKNING
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 1e8c4a53266072db71952ee35b15e5de54fabb95
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="tracking-user-behavior-inside-azure-ad-b2c-journeys-using-application-insights"></a>Spåra användarens beteende i Azure AD B2C-transporter med hjälp av Application Insights

Azure Active Directory B2C fungerar bra med Azure Application Insights.  De ger detaljerad och anpassade händelseloggar för din anpassade skapade användaren resor.  Den här guiden visar hur du kommer igång så att du kan: 
* få insikter om användarbeteende
* Felsöka dina egna principer under utveckling eller i produktion
* måttet prestanda
* skapa aviseringar från Application Insights

## <a name="how-it-works"></a>Hur det fungerar
En ny provider har lagts till Azure AD B2C identitet upplevelse Framework: `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Händelsedata skickas direkt till Application Insights med Instrumentation nyckel till Azure AD B2C.  En profil för tekniska använder den här providern för att definiera en händelse från B2C.  Profilen anger namnet på händelsen, vilka anspråk som kommer att spelas in och instrumentation nyckel.  Om du vill skicka en händelse tekniska profilen läggs sedan som är `orchestration step` eller som en `validation technical profile` i en anpassad användare resa.  Händelser kan enhetlig av Application Insights använder Korrelations-ID för att registrera en användarsession.  Application Insights gör händelse och session tillgänglig inom några sekunder och presenteras många visualiseringen export och analysverktyg.



## <a name="prerequisites"></a>Förutsättningar
Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md).  Den här guiden förutsätter att startpaket anpassad princip används.  Men det krävs inte.



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Steg 1. Skapa en Application Insights-resurs och hämta nyckel för Instrumentation

Application Insights är ett kraftfullt verktyg. När du använder det med Azure AD B2C, är det enda kravet att skapa en resurs och få en Instrumentation nyckel.  Application Insights måste skapas i den [Azure-portalen.](https://portal.azure.com)

[Fullständig dokumentation för Application Insights](https://docs.microsoft.com/azure/application-insights/)

1. Klicka på `+ Create a resource` i Azure-portalen i din prenumeration-klient.  Den här klienten är inte din Azure AD B2C-klient.  
2. Söka efter och välja `Application Insights`  
3. Skapa en resurs för `Application Type` `ASP.NET web application` under en prenumeration på dina inställningar.
4. När du skapat öppna Application Insights-resurs och Observera den  `Instrumentation Key` 

![Översikt över Application Insights och Instrumentation nyckel](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Steg 2. Lägga till nya ClaimType definitioner i filen förtroende Framework tillägg

### <a name="open-the-extension-file-from-the-starter-pack-and-add-the-following-elements-to-the-buildingblocks-node--the-extensions-filename-is-typically-yourtenantonmicrosoftcom-b2c1atrustframeworkextensionsxml"></a>Öppna tilläggsfilen från startpaket och Lägg till följande element till den `<BuildingBlocks>` nod.  Tillägg filnamnet är vanligtvis `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

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

Tekniska profiler kan anses funktioner i Azure AD B2C identitet upplevelse Framework.  Fem tekniska profiler definieras i det här exemplet att öppna en session och publicera händelser.

| Tekniska profil       | Aktivitet |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | gemensam uppsättning parametrar som ska inkluderas i alla Azure-Insights tekniska profiler     | 
| JourneyContextForInsights   | öppnar sessionen i appen insikter och skickar en Korrelations-ID |
| AzureInsights-SignInRequest     | skapar en ”inloggning” händelse med en uppsättning anspråk när en begäran om inloggning har tagits emot      | 
| AzureInsights-UserSignup | skapar en händelse som kallas ”UserSignup” när alternativet registreringen har utlösts av användaren i en registrering/signin-resa     | 
| AzureInsights-SignInComplete | registrerar slutförande av en autentisering när en token har skickats till förlitande partsprogram     | 

Lägg till profilerna i tilläggsfilen från startpaket genom att lägga till dessa element och den `<ClaimsProviders>` nod.  Tillägg filnamnet är vanligtvis `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Ändra den `Instrumentation Key` i den `ApplicationInsights-Common` tekniska profil till det GUID som angavs av Application Insights-resurs.

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
            <!-- 
                            An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
                            to create an event with the specified value.
                        -->
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
            <!-- 
                            A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
                            with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
                        -->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
                            A boolean indicating whether telemetry should be enabled or not.
                        -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
                            Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
                            to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
                        -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Steg 4. Lägg till tekniska profiler för Application Insights som orchestration-steg i en befintlig användare resa.

Anropa `JournyeContextForInsights` som orchestration-steg 1

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Anropa `Azure-Insights-SignInRequest` som orchestration steg 2 för att spåra en logga-i/sign-upp begäran har tagits emot.

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Omedelbart **innan** den `SendClaims` orchestration kan du lägga till ett nytt steg som anropar `Azure-Insights-UserSignup`. Utlöses när knappen registreringen har klickats på en registrering/inloggning resa.

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

Omedelbart efter den `SendClaims` orchestration-steg, anropet `Azure-Insights-SignInComplete`.   Det här steget kommer att användas i en har slutförts resa.

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> När du lägger till nya orchestration-steg numrera om stegen sekventiellt utan hoppar över alla heltal från 1 till N


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Steg 5. Överför tilläggsfilen ändrade, kör principen och visa händelser i Application Insights

Spara och ladda upp den nya betrodda framework-tillägg-filen.  Anropa sedan principen för förlitande part från ditt program eller Använd `Run Now` i Azure AD B2C-gränssnittet.  Inom några sekunder bör blir händelserna tillgängliga i Application Insights.

1. Öppna Application Insights-resurs i din Azure Active Directory-klient.
2. Klicka på `Events` i den `USAGE` undermenyer.
3. Ange `During` till `Last hour` och `By` till `3 minutes`.  Du kan behöva klicka på `Refresh` att visa resultat

![Application Insights-Användningshändelser Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  <a name="next-steps"></a>NÄSTA STEG

Lägga till ytterligare anspråkstyper och händelser i dina användare resa så att de passar dina behov.  Här är en lista över möjliga anspråk med ytterligare anspråk matchare.

### <a name="culture-specific-claims"></a>Kultur-specifika anspråk

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>Princip-specifika anspråk

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openidconnect-specific-claims"></a>OpenIDConnect särskilda anspråk

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Icke-protokollparametrar medföljer OIDC & OAuth2 begäranden

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Varje parameternamn som ingår i en OIDC eller OAuth2-begäran kan mappas till ett anspråk i användarens transporten.  Det kan sedan registreras i händelsen. Begäran från programmet kan till exempel innehålla en frågesträngparametern med namnet `app_session`, `loyalty_number` eller `any_string`.

Exempel på begäran från programmet:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Anspråk kan sedan läggas till genom att lägga till ett element för inkommande anspråk till Application Insights händelse med:
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
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



