---
title: "Application Insights för att felsöka principer för anpassad - Azure AD B2C | Microsoft Docs"
description: "hur du konfigurerar Application Insights för att spåra körningen av anpassade principer"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: saeda
ms.openlocfilehash: 8c79df33cd5f04f490e2cc6372f7e8ac1c4d9bbe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: Insamling av loggar

Den här artikeln innehåller steg för att samla in loggar från Azure AD B2C så att du kan felsöka problem med din anpassade principer.

>[!NOTE]
>För närvarande är utformade för detaljerad aktivitetsloggar som beskrivs här **bara** vid utveckling av anpassade principer. Använd inte utvecklingsläge i produktion.  Loggar samla in alla anspråk som skickas till och från identitetsleverantörer under utvecklingen.  Om används i produktionen ansvarar utvecklaren för personligt identifierbar information (privat identifierbar Information) som samlas in i loggen för App Insights som de äger.  Dessa detaljerade loggar samlas endast in när principen är placerad **UTVECKLINGSLÄGE**.


## <a name="use-application-insights"></a>Använda Application Insights

Azure AD B2C stöder en funktion för att skicka data till Application Insights.  Application Insights är ett sätt att diagnostisera undantag och visualisera prestandaproblem i programmet.

### <a name="setup-application-insights"></a>Installera Application Insights

1. Gå till [Azure-portalen](https://portal.azure.com). Se till att du är i klienten med din Azure-prenumeration (inte din Azure AD B2C-klient).
1. Klicka på **+ ny** i den vänstra navigeringsmenyn.
1. Söka efter och välja **Programinsikter**, klicka på **skapa**.
1. Fyll i formuläret och klicka på **skapa**. Välj **allmänna** för den **programtyp**.
1. När resursen har skapats kan du öppna Application Insights-resursen.
1. Hitta **egenskaper** i vänster-menyn och klicka på den.
1. Kopiera den **Instrumentation nyckeln** och spara den för nästa avsnitt.

### <a name="set-up-the-custom-policy"></a>Konfigurera en anpassad princip

1. Öppna filen RP (till exempel SignUpOrSignin.xml).
1. Lägg till följande attribut till den `<TrustFrameworkPolicy>` element:

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Om den inte redan finns, lägger du till en underordnad nod `<UserJourneyBehaviors>` till den `<RelyingParty>` nod. Det måste finnas omedelbart efter den`<DefaultUserJourney ReferenceId="YourPolicyName" />`
2. Lägg till följande nod som är underordnad den `<UserJourneyBehaviors>` element. Ersätt `{Your Application Insights Key}` med den **Instrumentation nyckeln** som du fick från Application Insights i föregående avsnitt.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"`Anger ApplicationInsights att påskynda telemetri via pipeline bearbetning bra för utveckling, men begränsad vid stora volymer.
  * `ClientEnabled="true"`skickar klientskript ApplicationInsights för spårning av klientsidan och visa sidfel (behövs inte).
  * `ServerEnabled="true"`skickar befintliga UserJourneyRecorder JSON som en anpassad händelse till Application Insights.
Exempel:

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
      <DefaultUserJourney ReferenceId="YourPolicyName" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. Överför principen.

### <a name="see-the-logs-in-application-insights"></a>Se loggarna i Application Insights

>[!NOTE]
> Det finns en kort fördröjning (mindre än fem minuter) innan du kan se nya loggar i Application Insights.

1. Öppna Application Insights-resursen som du skapade i den [Azure-portalen](https://portal.azure.com).
1. I den **översikt** -menyn klickar du på **Analytics**.
1. Öppna en ny flik i Application Insights.
1. Här är en lista över frågor som du kan använda för att visa loggar

| Fråga | Beskrivning |
|---------------------|--------------------|
spår | Visa alla loggar som genereras av Azure AD B2C |
spår \| där tidsstämpel > ago(1d) | Visa alla loggar som genereras av Azure AD B2C för den sista dagen

Posterna kan vara långt.  Exportera till CSV för en närmare titt.

Du kan lära dig mer om verktyget Analytics [här](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>Gemenskapen har utvecklat ett användaren resa visningsprogram att hjälpa utvecklare att identitet.  Den är inte stöds av Microsoft och göras tillgänglig strikt som-är.  Det läser från Application Insights-instans och innehåller en korrekt struktur för användaren resa händelser.  Du hämta källkoden och distribuerar den i din egen lösning.

>[!NOTE]
>För närvarande är utformade för detaljerad aktivitetsloggar som beskrivs här **bara** vid utveckling av anpassade principer. Använd inte utvecklingsläge i produktion.  Loggar samla in alla anspråk som skickas till och från identitetsleverantörer under utvecklingen.  Om används i produktionen ansvarar utvecklaren för personligt identifierbar information (privat identifierbar Information) som samlas in i loggen för App Insights som de äger.  Dessa detaljerade loggar samlas endast in när principen är placerad **UTVECKLINGSLÄGE**.

[Github-lagringsplatsen för exempel på stöds inte anpassade princip och relaterade verktyg](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Nästa steg

Utforska data i Application Insights som hjälper dig att förstå hur identitet upplevelse ramen underliggande B2C fungerar för att leverera din egen identitet upplevelsen.
