---
title: Felsöka anpassade principer med Application Insights
titleSuffix: Azure AD B2C
description: Konfigurera Application Insights för att spåra körningen av dina anpassade principer.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3aeef1614f22563e0fd348c5bc6ae7ff1e7b0b03
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848153"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Samla in Azure Active Directory B2C loggar med Application Insights

Den här artikeln innehåller steg för att samla in loggar från Active Directory B2C (Azure AD B2C) så att du kan diagnostisera problem med dina anpassade principer. Application Insights är ett sätt att diagnostisera undantag och visualisera problem med program prestanda. Azure AD B2C innehåller en funktion för att skicka data till Application Insights.

De detaljerade aktivitets loggarna som beskrivs här ska **bara** aktive ras under utvecklingen av dina anpassade principer.

> [!WARNING]
> Aktivera inte utvecklings läge i produktion. Loggar samlar in alla anspråk som skickas till och från identitets leverantörer. Du när utvecklaren antar ansvar för alla personliga data som samlas in i dina Application Insights loggar. Dessa detaljerade loggar samlas endast in när principen placeras i **utvecklarläge**.

## <a name="set-up-application-insights"></a>Konfigurera Application Insights

Om du inte redan har en, skapar du en instans av Application Insights i din prenumeration.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din Azure-prenumeration (inte din Azure AD B2C-katalog).
1. Välj **skapa en resurs** i den vänstra navigerings menyn.
1. Sök efter och välj **Application Insights**och välj sedan **skapa**.
1. Fyll i formuläret, Välj **Granska + skapa**och välj sedan **skapa**.
1. När distributionen har slutförts väljer **du gå till resurs**.
1. Under **Konfigurera** i Application Insights-menyn väljer du **Egenskaper**.
1. Registrera **Instrumentation-nyckeln** för användning i ett senare steg.

## <a name="configure-the-custom-policy"></a>Konfigurera den anpassade principen

1. Öppna den förlitande part filen (RP), till exempel *SignUpOrSignin. XML*.
1. Lägg till följande attribut i `<TrustFrameworkPolicy>`-elementet:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Om den inte redan finns lägger du till en `<UserJourneyBehaviors>` underordnad nod till noden `<RelyingParty>`. Den måste finnas omedelbart efter `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`.
1. Lägg till följande nod som underordnad till `<UserJourneyBehaviors>`-elementet. Se till att ersätta `{Your Application Insights Key}` med den Application Insights **Instrumentation-nyckel** som du spelade in tidigare.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` anger att ApplicationInsights ska påskynda Telemetrin genom bearbetnings pipelinen. Lämpligt för utveckling, men är begränsat till hög volym.
    * `ClientEnabled="true"` skickar skript för ApplicationInsights på klient sidan för att spåra sid visning och fel på klient sidan. Du kan visa dessa i tabellen **browserTimings** i Application Insights-portalen. Genom att ange `ClientEnabled= "true"`lägger du till Application Insights till sid skriptet och du får tids inställningar för sid inläsningar och AJAX-anrop, antal, information om webb läsar undantag och AJAX-fel samt antal användare och sessioner. Det här fältet är **valfritt**och är inställt på `false` som standard.
    * `ServerEnabled="true"` skickar den befintliga UserJourneyRecorder-JSON som en anpassad händelse till Application Insights.

    Ett exempel:

    ```XML
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Överför principen.

## <a name="see-the-logs-in-application-insights"></a>Se loggarna i Application Insights

Det finns en kort fördröjning, vanligt vis mindre än fem minuter, innan du kan se nya loggar i Application Insights.

1. Öppna Application Insights resurs som du skapade i [Azure Portal](https://portal.azure.com).
1. I **översikts** menyn väljer du **analys**.
1. Öppna en ny flik i Application Insights.

Här är en lista över frågor som du kan använda för att visa loggarna:

| Söka i data | Beskrivning |
|---------------------|--------------------|
`traces` | Se alla loggar som genererats av Azure AD B2C |
`traces | where timestamp > ago(1d)` | Se alla loggar som genererats av Azure AD B2C den senaste dagen

Posterna kan vara långa. Exportera till CSV för en närmare titt.

Mer information om frågor finns i [Översikt över logg frågor i Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Nästa steg

Communityn har utvecklat ett användar resa visnings program för att hjälpa identitets utvecklare. Den läser från din Application Insights instans och ger en välstrukturerad vy över användar resans händelser. Du får käll koden och distribuerar den i din egen lösning.

Användarens körnings spelare stöds inte av Microsoft och görs tillgänglig enbart i befintligt skick.

Du hittar den version av visnings programmet som läser händelser från Application Insights på GitHub, här:

[Azure-samples/Active-Directory-B2C-Advanced-policys](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
