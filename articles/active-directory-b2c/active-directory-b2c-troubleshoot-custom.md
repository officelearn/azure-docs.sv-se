---
title: Application Insights för att felsöka anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Så här konfigurerar du Application Insights för att spåra körning av anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9b25e5dc5d090ad7aab3d61e2c303a465b5d7443
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703925"
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: Samla in loggar

Den här artikeln innehåller steg för att samla in loggar från Azure AD B2C så att du kan diagnostisera problem med din anpassade principer.

>[!NOTE]
>För närvarande detaljerad aktivitetsloggar som beskrivs här är utformade **endast** att underlätta utvecklingen av anpassade principer. Använd inte utvecklingsläge i produktion.  Loggar samla in alla anspråk som skickas till och från identitetsleverantör man under utvecklingen.  Om används i produktion, ansvara utvecklaren för personligt identifierbar information (privat identifierbar Information) som samlas in i loggen för App Insights som de äger.  De här detaljerade loggar samlas endast när principen placeras på **UTVECKLINGSLÄGE**.


## <a name="use-application-insights"></a>Använda Application Insights

Azure AD B2C har stöd för en funktion för att skicka data till Application Insights.  Application Insights erbjuder ett sätt att diagnostisera undantag och visualisera problem med programprestanda.

### <a name="setup-application-insights"></a>Konfigurera Application Insights

1. Gå till [Azure-portalen](https://portal.azure.com). Kontrollera att du har klienten med din Azure-prenumeration (inte din Azure AD B2C-klient).
1. Klicka på **+ ny** i den vänstra navigeringsmenyn.
1. Sök efter och välj **Programinsikter**, klicka sedan på **skapa**.
1. Fyll i formuläret och klicka på **skapa**. Välj **Allmänt** för den **programtyp**.
1. När resursen har skapats kan du öppna Application Insights-resursen.
1. Hitta **egenskaper** i den vänstra menyn och klicka på det.
1. Kopiera den **Instrumenteringsnyckeln** och spara den till nästa avsnitt.

### <a name="set-up-the-custom-policy"></a>Konfigurera en anpassad princip

1. Öppna filen RP (till exempel SignUpOrSignin.xml).
1. Lägg till följande attribut till den `<TrustFrameworkPolicy>` element:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Om den inte redan finns, lägger du till en underordnad nod `<UserJourneyBehaviors>` till den `<RelyingParty>` noden. Det måste finnas direkt efter den `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`
2. Lägg till följande nod som en underordnad den `<UserJourneyBehaviors>` element. Ersätt `{Your Application Insights Key}` med den **Instrumenteringsnyckeln** som du fick från Application Insights i föregående avsnitt.

   ```XML
   <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   ```

   * `DeveloperMode="true"` Visar ApplicationInsights att skicka frekvensvärde via process-pipelinen bra för utveckling, men begränsad vid stora volymer.
   * `ClientEnabled="true"` skickar klientskript ApplicationInsights för att spåra vy och klientsidan sidfel (behövs inte).
   * `ServerEnabled="true"` skickar den befintliga UserJourneyRecorder JSON som en anpassad händelse till Application Insights.
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
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
   </TrustFrameworkPolicy>
   ```

3. Ladda upp principen.

### <a name="see-the-logs-in-application-insights"></a>Se loggar i Application Insights

>[!NOTE]
> Det finns en kort fördröjning (mindre än fem minuter) innan du kan se nya loggar i Application Insights.

1. Öppna Application Insights-resursen som du skapade i den [Azure-portalen](https://portal.azure.com).
1. I den **översikt** -menyn klickar du på **Analytics**.
1. Öppna en ny flik i Application Insights.
1. Här är en lista med frågor som du kan använda för att se loggar

| Fråga | Beskrivning |
|---------------------|--------------------|
spårningar | Se alla loggar som genereras av Azure AD B2C |
spårningar \| där timestamp > ago(1d) | Se alla loggar som genereras av Azure AD B2C för den sista dagen

Posterna kan vara långt.  Exportera till CSV för en närmare titt.

Du kan läsa mer om verktyget Analytics [här](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>Communityn har tagit fram ett användaren resa visningsprogram för att hjälpa utvecklare identitet.  Det är inte stöds av Microsoft och tillgänglig strikt som – är.  Den läser från din Application Insights-instans och innehåller en välstrukturerade av användaren resa händelser.  Du hämta källkoden och distribuera den på din egen lösning.

Versionen av visningsprogrammet som läser händelser från Application Insights finns [här](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>För närvarande detaljerad aktivitetsloggar som beskrivs här är utformade **endast** att underlätta utvecklingen av anpassade principer. Använd inte utvecklingsläge i produktion.  Loggar samla in alla anspråk som skickas till och från identitetsleverantör man under utvecklingen.  Om används i produktion, ansvara utvecklaren för personligt identifierbar information (privat identifierbar Information) som samlas in i loggen för App Insights som de äger.  De här detaljerade loggar samlas endast när principen placeras på **UTVECKLINGSLÄGE**.

[GitHub-lagringsplatsen för stöds inte anpassade princip exempel och relaterade verktyg](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Nästa steg

Utforska data i Application Insights för att hjälpa dig att förstå hur Identitetsramverk underliggande B2C fungerar för att leverera din egen identitet inträffar.
