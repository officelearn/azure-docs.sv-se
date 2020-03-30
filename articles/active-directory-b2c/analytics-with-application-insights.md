---
title: Spåra användarbeteende med Application Insights
titleSuffix: Azure AD B2C
description: Lär dig hur du aktiverar händelseloggar i Application Insights från Azure AD B2C-användarresor med hjälp av anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 03/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 687c9620ae70f7bca2b95a94dd8fe411d7348b30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246491"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Spåra användarbeteende i Azure Active Directory B2C med hjälp av Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) stöder att skicka händelsedata direkt till [Application Insights](../azure-monitor/app/app-insights-overview.md) med hjälp av instrumenteringsnyckeln som tillhandahålls till Azure AD B2C.  Med en teknisk profil för Application Insights kan du få detaljerade och anpassade händelseloggar för dina användarresor till:

* Få insikter om användarbeteende.
* Felsöka dina egna principer i utveckling eller i produktion.
* Mät prestanda.
* Skapa meddelanden från Application Insights.

## <a name="how-it-works"></a>Hur det fungerar

Den tekniska profilen [Application Insights](application-insights-technical-profile.md) definierar en händelse från Azure AD B2C. Profilen anger namnet på händelsen, anspråken som registreras och instrumenteringsnyckeln. Om du vill bokföra en händelse läggs den tekniska profilen sedan till som ett orchestration-steg i en [användarfärd](userjourneys.md).

Application Insights kan förena händelserna genom att använda ett korrelations-ID för att spela in en användarsession. Application Insights gör händelsen och sessionen tillgänglig inom några sekunder och presenterar många visualiserings-, export- och analysverktyg.

## <a name="prerequisites"></a>Krav

Slutför stegen i [Komma igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

När du använder Application Insights med Azure AD B2C behöver du bara skapa en resurs och hämta instrumenteringsnyckeln. Information finns i [Skapa en programinsiktsresurs](../azure-monitor/app/create-new-resource.md)

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder katalogen som innehåller din Azure-prenumeration genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din prenumeration. Den här klienten är inte din Azure AD B2C-klient.
3. Välj **Skapa en resurs** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Programinsikter**.
4. Klicka på **Skapa**.
5. Ange ett **namn** för resursen.
6. För **programtyp**väljer du **ASP.NET webbprogram**.
7. För **Resursgrupp**väljer du en befintlig grupp eller anger ett namn för en ny grupp.
8. Klicka på **Skapa**.
4. När du har skapat application insights-resursen öppnar du den, expanderar **Essentials**och kopierar instrumenteringsnyckeln.

![Översikt över programinsikter och instrumenteringsnyckel](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definiera anspråk

Ett anspråk ger en tillfällig lagring av data under en Azure AD B2C-principkörning. [Skadeschemat](claimsschema.md) är den plats där du deklarerar dina anspråk.

1. Öppna filen för tillägg i principen. Till exempel <em> `SocialAndLocalAccounts/` </em>.
1. Sök efter elementet [BuildingBlocks.](buildingblocks.md) Om elementet inte finns lägger du till det.
1. Leta reda på elementet [ClaimsSchema.](claimsschema.md) Om elementet inte finns lägger du till det.
1. Lägg till följande anspråk i elementet **ClaimsSchema.** 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Lägg till nya tekniska profiler

Tekniska profiler kan betraktas som funktioner i Identity Experience Framework i Azure AD B2C. Den här tabellen definierar de tekniska profiler som används för att öppna en session och publicera händelser.

| Teknisk profil | Aktivitet |
| ----------------- | -----|
| AppInsights-Common | Den gemensamma uppsättningen parametrar som ska ingå i alla tekniska azure insights-profiler. |
| AppInsights-SignInRequest | Registrerar `SignInRequest` en händelse med en uppsättning anspråk när en inloggningsbegäran har tagits emot. |
| AppInights-UserSignUp | Registrerar `UserSignUp` en händelse när användaren utlöser registreringsalternativet i en registrerings-/inloggningsresa. |
| AppInsights-SignInComplete | Registrerar `SignInComplete` en händelse när en autentisering har slutförts när en token har skickats till det förlitande part-programmet. |

Lägg till profilerna i filen *TrustFrameworkExtensions.xml* från startpaketet. Lägg till dessa element i elementet **ClaimsProviders:**

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Ändra instrumenteringsnyckeln i den `AppInsights-Common` tekniska profilen till det GUID som application insights-resursen tillhandahåller.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Lägg till de tekniska profilerna som orchestration-steg

Anropa `AppInsights-SignInRequest` som orkestrering steg 2 för att spåra att en inloggnings-/registreringsbegäran har tagits emot:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Omedelbart *before* före `SendClaims` orchestration-steget lägger du `AppInsights-UserSignup`till ett nytt steg som anropar . Den utlöses när användaren väljer registreringsknappen i en registrerings-/inloggningsresa.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Omedelbart efter `SendClaims` orchestration-steget `AppInsights-SignInComplete`ringer du . Det här steget visar en lyckad färd.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> När du har lagt till de nya orchestration-stegen numrerar du om stegen sekventiellt utan att hoppa över några heltal från 1 till N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Ladda upp filen, kör principen och visa händelser

Spara och ladda upp *filen TrustFrameworkExtensions.xml.* Anropa sedan den förlitande partprincipen från ditt program eller använd **Kör nu** i Azure-portalen. I sekunder är dina händelser tillgängliga i Application Insights.

1. Öppna **application insights-resursen** i din Azure Active Directory-klient.
2. Välj **användningshändelser** > **Events**.
3. Ställ in **under** till **sista timmen** och **med** till **3 minuter**.  Du kan behöva välja **Uppdatera för** att visa resultat.

![Programinsikter ANVÄNDNING-Händelser Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>[Valfritt] Samla in mer data

Lägg till anspråkstyper och händelser i din användarresa för att passa dina behov. Du kan använda [anspråkslösare](claim-resolver-overview.md) eller någon sträng anspråkstyp, lägga till anspråk genom att lägga till ett **indataanspråkselement** i application insights-händelsen eller i den tekniska profilen AppInsights-Common.

- **ClaimTypeReferenceId** är referensen till en anspråkstyp.
- **PartnerClaimType** är namnet på egenskapen som visas i Azure Insights. Använd syntaxen `{property:NAME}`för `NAME` , var är egenskapen läggs till i händelsen.
- **DefaultValue** använder vilket strängvärde eller anspråksmatcharen som helst.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om den tekniska profilen [för Application Insights](application-insights-technical-profile.md) i IEF-referensen. 