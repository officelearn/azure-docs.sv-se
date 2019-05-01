---
title: Spåra användarnas beteende med hjälp av händelser i Application Insights från Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du aktiverar händelseloggar i Application Insights från Azure AD B2C användaren resor med anpassade principer (förhandsversion).
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8284be21db01ed7bd3215f7a67c8bfb40e0d73de
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705146"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Spåra användarnas beteende i Azure Active Directory B2C med Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

När du använder Azure Active Directory (Azure AD) B2C tillsammans med Azure Application Insights kan du kan få detaljerad och anpassade händelseloggar för dina användare resor. I den här artikeln kan du se hur du:

* Få insikter om användarbeteende.
* Felsöka dina egna principer i utveckling eller produktion.
* Mäta prestanda.
* Skapa aviseringar från Application Insights.

## <a name="how-it-works"></a>Hur det fungerar

Identitetsramverk i Azure AD B2C innehåller providern `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Händelsedata skickas direkt till Application Insights med hjälp av instrumentationsnyckeln tillhandahålls till Azure AD B2C.

Tekniska profilen använder den här providern för att definiera en händelse från Azure AD B2C. Profilen anger namnet på händelsen, de anspråk som registreras och instrumenteringsnyckeln. Om du vill publicera en händelse läggs sedan den tekniska profilen som en `orchestration step`, eller som en `validation technical profile` i en anpassad användarresa.

Application Insights kan en enhetlig händelser med ett Korrelations-ID för att registrera en användarsession. Application Insights gör den händelsen och sessionen tillgänglig inom några sekunder och visar många visualisering, export och analysverktyg.

## <a name="prerequisites"></a>Nödvändiga komponenter

Utför stegen i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md). Den här artikeln förutsätter att du använder startpaket för anpassad princip. Men startpaket krävs inte.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

När du använder Application Insights med Azure AD B2C är allt du behöver göra skapa en resurs och få instrumenteringsnyckeln.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Kontrollera att du använder den katalog som innehåller din Azure-prenumeration genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din prenumeration. Den här klienten är inte din Azure AD B2C-klient.
3. Välj **skapa en resurs** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Application Insights**.
4. Klicka på **Skapa**.
5. Ange en **namn** för resursen.
6. För **programtyp**väljer **ASP.NET-webbprogram**.
7. För **resursgrupp**, Välj en befintlig grupp eller ange ett namn för en ny grupp.
8. Klicka på **Skapa**.
4. När du skapar Application Insights-resursen, öppna den, expanderar **Essentials**, och kopierar instrumenteringsnyckeln.

![Översikt över Application Insights och Instrumenteringsnyckeln](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Lägg till nya ClaimType definitioner

Öppna den *TrustFrameworkExtensions.xml* filen från startpaket och Lägg till följande element i den [BuildingBlocks](buildingblocks.md) element:

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

## <a name="add-new-technical-profiles"></a>Lägga till nya tekniska profiler

Tekniska profiler kan ses funktioner i den identitet upplevelse Framework av Azure AD B2C. Den här tabellen definieras de tekniska profiler som används för att öppna en session och publicerar händelser.

| Tekniska profilen | Aktivitet |
| ----------------- | -----|
| AzureInsights-Common | Skapar en gemensam uppsättning parametrar som ska ingå i alla AzureInsights tekniska profiler. | 
| AzureInsights-SignInRequest | Skapar en inloggning från händelse med en uppsättning anspråk när en inloggningsbegäran har tagits emot. | 
| AzureInsights-UserSignup | Skapar en UserSignup händelse när användaren utlöser inloggningsalternativet i en registrerings-registreringen/inloggning resa. | 
| AzureInsights-SignInComplete | Registrerar en autentisering slutförs när en token har skickats till den förlitande partsprogram. | 

Lägga till profiler till den *TrustFrameworkExtensions.xml* filen från startpaket. Lägg till dessa element och den **ClaimsProviders** element:

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
> Ändra instrumenteringsnyckeln i den `ApplicationInsights-Common` tekniska profil till det GUID som innehåller Application Insights-resursen.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Lägg till de tekniska profilerna som orchestration-steg

Anropa `Azure-Insights-SignInRequest` som orchestration steg 2 för att spåra en inloggning-i/registrering begäran har tagits emot:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Omedelbart *innan* den `SendClaims` orkestrering kan du lägga till ett nytt steg som anropar `Azure-Insights-UserSignup`. Utlöses när användaren väljer knappen registrera dig i en registrerings-registreringen/inloggning resa.

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

Direkt efter den `SendClaims` orkestreringssteget, anrop `Azure-Insights-SignInComplete`. Det här steget visar en resa som har slutförts.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> När du har lagt till nya orchestration-steg kan du numrera om stegen sekventiellt utan hoppar över alla heltal från 1 till N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Ladda upp din fil, köra principen och granska händelser

Spara och ladda upp den *TrustFrameworkExtensions.xml* fil. Anropa sedan principen för förlitande part från ditt program eller Använd **kör nu** i Azure-portalen. Händelser är tillgängliga i Application Insights i sekunder.

1. Öppna den **Application Insights** resurs i Azure Active Directory-klient.
2. Välj **användning** > **händelser**.
3. Ange **under** till **senaste timmen** och **av** till **3 minuter**.  Du kan behöva välja **uppdatera** att visa resultat.

![Application Insights-Användningshändelser Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Nästa steg

Lägg till anspråkstyper och händelser användarresan som passar dina behov. Du kan använda [anspråk matchare](claim-resolver-overview.md) eller valfri sträng Anspråkstyp, lägga till anspråk genom att lägga till en **inkommande anspråk** element till Application Insights-händelse eller till den tekniska profilen AzureInsights gemensamma. 

- **ClaimTypeReferenceId** är referens till en anspråkstyp.
- **PartnerClaimType** är namnet på den egenskap som visas i Azure Insights. Använd syntaxen för `{property:NAME}`, där `NAME` är egenskap som läggs till händelsen. 
- **DefaultValue** använda valfritt strängvärde eller matcharen anspråk. 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

