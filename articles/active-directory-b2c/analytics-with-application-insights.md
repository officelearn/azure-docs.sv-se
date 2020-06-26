---
title: Spåra användar beteende med Application Insights
titleSuffix: Azure AD B2C
description: Lär dig hur du aktiverar händelse loggar i Application Insights från Azure AD B2C användar resor med anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67ea7324419d86fa5b5c23a2f0aa5f8c057495d1
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85385985"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Spåra användar beteende i Azure Active Directory B2C att använda Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) stöder sändning av händelse data direkt till [Application Insights](../azure-monitor/app/app-insights-overview.md) med hjälp av Instrumentation-nyckeln som är avsedd att Azure AD B2C.  Med en Application Insights teknisk profil kan du hämta detaljerade och anpassade händelse loggar för dina användar resor till:

* Få insikter om användar beteende.
* Felsök dina egna principer i utvecklingen eller i produktionen.
* Mät prestanda.
* Skapa meddelanden från Application Insights.

## <a name="how-it-works"></a>Så här fungerar det

[Application Insights](application-insights-technical-profile.md) teknisk profil definierar en händelse från Azure AD B2C. Profilen anger namnet på händelsen, anspråken som registreras och Instrumentation-nyckeln. Om du vill publicera en händelse läggs den tekniska profilen till som ett Orchestration-steg i en [användar resa](userjourneys.md).

Application Insights kan förena händelserna genom att använda ett korrelations-ID för att registrera en användarsession. Application Insights gör händelsen och sessionen tillgänglig inom några sekunder och visar många visualiserings-, export-och analys verktyg.

## <a name="prerequisites"></a>Krav

Slutför stegen i [Kom igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

När du använder Application Insights med Azure AD B2C behöver du bara skapa en resurs och hämta Instrumentation-nyckeln. Mer information finns i [skapa en Application Insights resurs](../azure-monitor/app/create-new-resource.md)

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure-prenumeration genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din prenumeration. Den här klienten är inte din Azure AD B2C klient.
3. Välj **skapa en resurs** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Application Insights**.
4. Klicka på **Skapa**.
5. Ange ett **namn** för resursen.
6. För **program typ**väljer du **ASP.NET-webbprogram**.
7. För **resurs grupp**väljer du en befintlig grupp eller anger ett namn för en ny grupp.
8. Klicka på **Skapa**.
4. När du har skapat Application Insights-resursen öppnar du den, expanderar **Essentials**och kopierar Instrumentation-nyckeln.

![Application Insights översikt och Instrumentation-nyckel](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definiera anspråk

Ett anspråk ger tillfällig lagring av data under en Azure AD B2C princip körning. [Anspråks schema](claimsschema.md) är den plats där du deklarerar dina anspråk.

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ClaimsSchema](claimsschema.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråk i **ClaimsSchema** -elementet. 

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

Tekniska profiler kan betraktas som funktioner i Azure AD B2C för identitets upplevelsen. I den här tabellen definieras de tekniska profiler som används för att öppna en session och publicera händelser.

| Teknisk profil | Uppgift |
| ----------------- | -----|
| AppInsights – common | Den gemensamma uppsättningen parametrar som ska ingå i alla Azure Insights-tekniska profiler. |
| AppInsights – SignInRequest | Registrerar en `SignInRequest` händelse med en uppsättning anspråk när en inloggnings förfrågan har mottagits. |
| AppInsights – UserSignUp | Registrerar en `UserSignUp` händelse när användaren utlöser inloggnings alternativet i en inloggnings-eller inloggnings resa. |
| AppInsights – SignInComplete | Registrerar en `SignInComplete` händelse när en autentisering har slutförts, när en token har skickats till det förlitande part programmet. |

Lägg till profilerna i *TrustFrameworkExtensions.xml* -filen från start paketet. Lägg till dessa element i **ClaimsProviders** -elementet:

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
      </InputClaims>
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
> Ändra Instrumentation-nyckeln i den `AppInsights-Common` tekniska profilen till det GUID som Application Insights resursen tillhandahåller.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Lägg till de tekniska profilerna som Orchestration-steg

Anropa `AppInsights-SignInRequest` som dirigerings steg 2 för att spåra att en begäran om inloggning/registrering har tagits emot:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Omedelbart *före* `SendClaims` Orchestration-steget lägger du till ett nytt steg som anropar `AppInsights-UserSignup` . Den utlöses när användaren väljer knappen Registrera i en resa för registrering/inloggning.

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

Direkt efter `SendClaims` Orchestration-steget anropa `AppInsights-SignInComplete` . Det här steget visar att resan har slutförts.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> När du har lagt till de nya stegen för dirigering kan du numrera om stegen sekventiellt utan att hoppa över heltal från 1 till N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Ladda upp filen, kör principen och Visa händelser

Spara och ladda upp *TrustFrameworkExtensions.xml* -filen. Anropa sedan den förlitande part principen från programmet eller Använd **Kör nu** i Azure Portal. I sekunder är dina händelser tillgängliga i Application Insights.

1. Öppna **Application Insights** resursen i Azure Active Directory klient organisationen.
2. Välj **användnings**  >  **händelser**.
3. Anges **under** till **sista timmen** och **med** **3 minuter**.  Du kan behöva välja **Uppdatera** för att visa resultatet.

![Application Insights användning – händelser Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>Valfritt Samla in mer data

Lägg till anspråks typer och händelser till din användar resa för att passa dina behov. Du kan använda [anspråks matchare](claim-resolver-overview.md) eller valfri typ av sträng anspråk, lägga till anspråken genom att lägga till ett **indatamängds** element i Application Insights-händelsen eller till AppInsights-vanliga tekniska profiler.

- **ClaimTypeReferenceId** är referensen till en anspråks typ.
- **PartnerClaimType** är namnet på den egenskap som visas i Azure Insights. Använd syntaxen för `{property:NAME}` , där `NAME` är egenskapen som läggs till i händelsen.
- **Standardvärde** Använd valfritt sträng värde eller anspråks lösare.

```xml
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Application Insights](application-insights-technical-profile.md) teknisk profil i IEF-referensen. 
