---
title: Spåra användar beteende med Application Insights
titleSuffix: Azure AD B2C
description: Lär dig hur du aktiverar händelse loggar i Application Insights från Azure AD B2C användar resor med anpassade principer.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8376deecb5e184c01b41495b868b57bd8fd745d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367968"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Spåra användar beteende i Azure Active Directory B2C att använda Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

När du använder Azure Active Directory B2C (Azure AD B2C) tillsammans med Azure Application insikter kan du få detaljerade och anpassade händelse loggar för dina användar resor. I den här artikeln kan du se hur du:

* Få insikter om användar beteende.
* Felsök dina egna principer i utvecklingen eller i produktionen.
* Mät prestanda.
* Skapa meddelanden från Application Insights.

## <a name="how-it-works"></a>Så här fungerar det

I Azure AD B2C för identitets miljö i ingår providern `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Den skickar händelse data direkt till Application Insights med hjälp av Instrumentation-nyckeln som Azure AD B2C.

En teknisk profil använder den här providern för att definiera en händelse från Azure AD B2C. Profilen anger namnet på händelsen, anspråken som registreras och Instrumentation-nyckeln. Om du vill publicera en händelse läggs den tekniska profilen till som en `orchestration step` i en anpassad användar resa.

Application Insights kan förena händelserna genom att använda ett korrelations-ID för att registrera en användarsession. Application Insights gör händelsen och sessionen tillgänglig inom några sekunder och visar många visualiserings-, export-och analys verktyg.

## <a name="prerequisites"></a>Krav

Slutför stegen i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md). I den här artikeln förutsätter vi att du använder start paketet för den anpassade principen. Men start paketet är inte obligatoriskt.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

När du använder Application Insights med Azure AD B2C behöver du bara skapa en resurs och hämta Instrumentation-nyckeln.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure-prenumeration genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din prenumeration. Den här klienten är inte din Azure AD B2C klient.
3. Välj **skapa en resurs** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Application Insights**.
4. Klicka på **Skapa**.
5. Ange ett **namn** för resursen.
6. För **program typ**väljer du **ASP.NET-webbprogram**.
7. För **resurs grupp**väljer du en befintlig grupp eller anger ett namn för en ny grupp.
8. Klicka på **Skapa**.
4. När du har skapat Application Insights-resursen öppnar du den, expanderar **Essentials**och kopierar Instrumentation-nyckeln.

![Application Insights översikt och Instrumentation-nyckel](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Lägg till nya definitioner för ClaimType

Öppna filen *TrustFrameworkExtensions. XML* från start paketet och Lägg till följande element i [BuildingBlocks](buildingblocks.md) -elementet:

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

## <a name="add-new-technical-profiles"></a>Lägg till nya tekniska profiler

Tekniska profiler kan betraktas som funktioner i Azure AD B2C för identitets upplevelsen. I den här tabellen definieras de tekniska profiler som används för att öppna en session och publicera händelser.

| Teknisk profil | Aktivitet |
| ----------------- | -----|
| AzureInsights-Common | Skapar en gemensam uppsättning parametrar som ska ingå i alla AzureInsights tekniska profiler. |
| AzureInsights-SignInRequest | Skapar en inloggnings händelse med en uppsättning anspråk när en inloggnings förfrågan har mottagits. |
| AzureInsights-UserSignup | Skapar en UserSignup-händelse när användaren utlöser inloggnings alternativet i en inloggnings-eller inloggnings resa. |
| AzureInsights-SignInComplete | Registrerar slutförd slutförd autentisering när en token har skickats till den förlitande parten. |

Lägg till profilerna i *TrustFrameworkExtensions. XML-* filen från start paketet. Lägg till dessa element i **ClaimsProviders** -elementet:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
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

> [!IMPORTANT]
> Ändra Instrumentation-nyckeln i `AzureInsights-Common` teknisk profil till det GUID som din Application Insights-resurs tillhandahåller.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Lägg till de tekniska profilerna som Orchestration-steg

Anropa `Azure-Insights-SignInRequest` som dirigerings steg 2 för att spåra att en begäran om inloggning/registrering har tagits emot:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Omedelbart *före* `SendClaims` Orchestration-steget lägger du till ett nytt steg som anropar `Azure-Insights-UserSignup`. Den utlöses när användaren väljer knappen Registrera i en resa för registrering/inloggning.

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Anropa `Azure-Insights-SignInComplete`direkt efter `SendClaims` Orchestration-steget. Det här steget visar att resan har slutförts.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> När du har lagt till de nya stegen för dirigering kan du numrera om stegen sekventiellt utan att hoppa över heltal från 1 till N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Ladda upp filen, kör principen och Visa händelser

Spara och ladda upp filen *TrustFrameworkExtensions. XML* . Anropa sedan den förlitande part principen från programmet eller Använd **Kör nu** i Azure Portal. I sekunder är dina händelser tillgängliga i Application Insights.

1. Öppna **Application Insights** resursen i Azure Active Directory klient organisationen.
2. Välj **användnings** > **händelser**.
3. Anges **under** till **sista timmen** och **med** **3 minuter**.  Du kan behöva välja **Uppdatera** för att visa resultatet.

![Application Insights användning – händelser Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Nästa steg

Lägg till anspråks typer och händelser till din användar resa för att passa dina behov. Du kan använda [anspråks matchare](claim-resolver-overview.md) eller valfri typ av sträng anspråk, lägga till anspråken genom att lägga till ett **indatamängds** element i Application Insights-händelsen eller till AzureInsights-vanliga tekniska profiler.

- **ClaimTypeReferenceId** är referensen till en anspråks typ.
- **PartnerClaimType** är namnet på den egenskap som visas i Azure Insights. Använd syntaxen för `{property:NAME}`, där `NAME` är egenskapen som läggs till i händelsen.
- **Standardvärde** Använd valfritt sträng värde eller anspråks lösare.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

