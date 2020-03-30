---
title: Felsöka anpassade principer med Application Insights
titleSuffix: Azure AD B2C
description: Så här konfigurerar du Programinsikter för att spåra körningen av dina anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186275"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Samla in Azure Active Directory B2C-loggar med Application Insights

Den här artikeln innehåller steg för att samla in loggar från Active Directory B2C (Azure AD B2C) så att du kan diagnostisera problem med dina anpassade principer. Application Insights är ett sätt att diagnostisera undantag och visualisera problem med programmets prestanda. Azure AD B2C innehåller en funktion för att skicka data till Application Insights.

De detaljerade aktivitetsloggar som beskrivs här bör **endast** aktiveras under utvecklingen av dina anpassade principer.

> [!WARNING]
> Aktivera inte utvecklingsläge i produktionen. Loggar samlar in alla anspråk som skickas till och från identitetsleverantörer. Du som utvecklare tar ansvar för alla personuppgifter som samlas in i dina Application Insights-loggar. Dessa detaljerade loggar samlas endast in när principen placeras i **DEVELOPER MODE**.

## <a name="set-up-application-insights"></a>Konfigurera programinsikter

Om du inte redan har en, skapa en instans av Application Insights i din prenumeration.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure-prenumeration (inte din Azure AD B2C-katalog).
1. Välj **Skapa en resurs** på menyn för vänster navigering.
1. Sök efter och välj **Programstatistik**och välj sedan **Skapa**.
1. Fyll i formuläret, välj **Granska + skapa**och välj sedan **Skapa**.
1. När distributionen har slutförts väljer du **Gå till resurs**.
1. Välj **Egenskaper**under **Konfigurera** på menyn Programinsikter .
1. Registrera **INSTRUMENTERINGSNYCKELN** för användning i ett senare steg.

## <a name="configure-the-custom-policy"></a>Konfigurera den anpassade principen

1. Öppna den förlitande partfilen (RP), till exempel *SignUpOrSignin.xml*.
1. Lägg till följande attribut `<TrustFrameworkPolicy>` i elementet:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Om den inte redan finns `<UserJourneyBehaviors>` lägger du `<RelyingParty>` till en underordnad nod i noden. Det måste placeras `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`omedelbart efter .
1. Lägg till följande nod som `<UserJourneyBehaviors>` underordnad för elementet. Se till `{Your Application Insights Key}` att ersätta med Application Insights **Instrumentation Key** som du spelade in tidigare.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`säger till ApplicationInsights att påskynda telemetrin genom bearbetningspipelinen. Bra för utvecklingen, men begränsad till höga volymer.
    * `ClientEnabled="true"`skickar skriptet ApplicationInsights på klientsidan för spårning av sidvisning och fel på klientsidan. Du kan visa dessa i tabellen **browserTimings** i portalen Application Insights. Genom `ClientEnabled= "true"`att ställa in lägger du till Application Insights till din sida skript och du får timings av sidan laster och AJAX samtal, räknas, information om webbläsarundantag och AJAX misslyckanden, och användare och session räknas. Det här fältet är **valfritt**och är inställt på som `false` standard.
    * `ServerEnabled="true"`skickar den befintliga UserJourneyRecorder JSON som en anpassad händelse till Application Insights.

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

1. Ladda upp principen.

## <a name="see-the-logs-in-application-insights"></a>Se loggarna i Application Insights

Det är en kort fördröjning, vanligtvis mindre än fem minuter, innan du kan se nya loggar i Application Insights.

1. Öppna application insights-resursen som du skapade i [Azure-portalen](https://portal.azure.com).
1. Välj **Analytics**på **översiktsmenyn** .
1. Öppna en ny flik i Application Insights.

Här är en lista över frågor som du kan använda för att se loggarna:

| Söka i data | Beskrivning |
|---------------------|--------------------|
`traces` | Se alla loggar som genereras av Azure AD B2C |
`traces | where timestamp > ago(1d)` | Se alla loggar som genereras av Azure AD B2C för den sista dagen

Posterna kan vara långa. Exportera till CSV för en närmare titt.

Mer information om frågor finns [i Översikt över loggfrågor i Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Nästa steg

Communityn har utvecklat en användare resa betraktaren för att hjälpa identitetsutvecklare. Den läser från din Application Insights-instans och ger en välstrukturerad vy över användarfärdhändelserna. Du får källkoden och distribuera den i din egen lösning.

Användarens färdspelare stöds inte av Microsoft och görs tillgänglig strikt som den är.

Du hittar den version av visningsprogrammet som läser händelser från Application Insights på GitHub här:

[Azure-Samples/active-directory-b2c-advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
