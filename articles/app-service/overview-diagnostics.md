---
title: Översikt av Azure App Service-diagnostik | Microsoft Docs
description: Lär dig hur du kan felsöka problem med din app med App Service-diagnostik.
keywords: app service, azure app service, diagnostics, support, web app, troubleshooting, self-help
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: f2f798be85e9c3aeb8d4b54cba89d8be059427e0
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147346"
---
# <a name="azure-app-service-diagnostics-overview"></a>Översikt av Azure App Service-diagnostik

När du kör ett webbprogram, som du vill att förbereda för eventuella problem som kan uppstå i 500-fel till användarna om att webbplatsen inte fungerar. App Service-diagnostik är en intelligent och interaktiv upplevelse för att felsöka din app med krävs ingen konfiguration. När du stöter på problem med din app, pekar App Service-diagnostik ut fel att guida dig till rätt information för att enkelt och snabbt felsöka och lösa problemet.

Även om detta är användbart när du har problem med din app under de senaste 24 timmarna, är alla diagnostiska diagram alltid tillgängliga för dig att analysera.

App Service-diagnostik passar inte bara din app på Windows, utan också appar på [Linux/behållare](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro), och [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Öppna App Service-diagnostik

Om du vill få åtkomst till App Service-diagnostik, navigera till din App Service-webbapp eller en App Service Environment i den [Azure-portalen](https://portal.azure.com). I det vänstra navigeringsfönstret klickar du på **diagnostisera och lösa problem**.

Azure Functions går du till din funktionsapp och i det övre navigeringsfältet, klickar du på **plattformsfunktioner**, och välj **diagnostisera och lösa problem** från den **resurshantering** avsnittet.

Du kan välja den kategori som bäst beskriver problemet med din app med hjälp av nyckelord i varje panel för startsidan i App Service-diagnostik startsidan. Den här sidan är också där du hittar **diagnostikverktyg** för Windows-appar. Se [diagnostiska verktyg (endast för Windows-app)](#diagnostic-tools-only-for-windows-app).

![Startsida](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interaktivt gränssnitt

När du har valt en webbsida kategori som bäst överensstämmer med din app problemet hjälper App Service diagnostics interaktivt gränssnitt, Genie dig att diagnostisera och lösa problem med din app. Du kan använda panelen genvägarna tillhandahålls av Genie för att visa den fullständiga diagnostikrapport av problem-kategori som du är intresserad av. Panelen genvägar ger dig ett enkelt sätt för att komma åt din diagnostikmått.

![Panelen genvägar](./media/app-service-diagnostics/tile-shortcuts-2.png)

När du klickar på dessa paneler, kan du se en lista över ämnen som rör problemet som beskrivs i panelen. Dessa avsnitt innehåller viktig information från den fullständiga rapporten kodfragment. Du kan klicka på någon av följande avsnitt för att undersöka problemet ytterligare. Dessutom kan du klicka på **Visa fullständig rapport** att utforska alla avsnitt på en enda sida.

![Ämnen](./media/app-service-diagnostics/application-logs-insights-3.png)

![Visa fullständig rapport](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnostikrapport

När du har valt att undersöka problemet ytterligare genom att klicka på ett ämne kan du visa mer information om ämnet ofta kompletteras med grafik och markdowns. Diagnostisk rapport kan vara ett kraftfullt verktyg för att hitta problem med din app.

![Diagnostikrapport](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Hälsokontroll

Om du inte vet vad är fel med din app eller vet inte var du ska börja felsöka ditt problem med är hälsokontroll ett bra ställe att börja. Hälsokontroll analyserar dina program att ge dig en snabba, interaktiva översikten som pekar på vad som är felfri och fel, om var du vill undersöka problemet. Dess intelligenta och interaktiva gränssnittet får du vägledning genom felsökningen. Hälsokontroll är integrerad med Genie upplevelse för Windows-appar och webbapp ned diagnostisk rapport för Linux-appar.

### <a name="health-checkup-graphs"></a>Hälsotillstånd checkup diagram

Det finns fyra olika diagram i hälsokontroll.

- **förfrågningar och fel:** Ett diagram som visar antalet begäranden som görs under de senaste 24 timmarna tillsammans med HTTP-serverfel.
- **prestanda:** Ett diagram som visar svarstid under de senaste 24 timmarna för olika: e percentilen grupper.
- **CPU-användning:** Ett diagram som visar den totala procent CPU-användningen per instans under de senaste 24 timmarna.  
- **minnesanvändning:** Ett diagram som visar den övergripande procent fysisk minnesanvändningen per instans under de senaste 24 timmarna.

![Hälsokontroll](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Undersöka koden programproblem (endast för Windows-app)

Eftersom många app-problemen är relaterade till problem i programkoden, App Service-diagnostik kan integreras med [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) att markera undantag och beroendeproblem att korrelera med den valda stilleståndstiden. Application Insights har aktiveras separat.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Om du vill visa Application Insights-undantag och beroenden, Välj den **webbapp ned** eller **webbappen är långsam** panelen genvägar.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Felsökningssteg (endast för Windows-app)

Om ett problem har identifierats med ett specifikt problem-kategori under de senaste 24 timmarna, du kan visa den fullständiga Diagnostikrapporten och App Service-diagnostik kan bli ombedd att visa mer felsökningstips och nästa steg för en mer interaktiv upplevelse.

![Application Insights och felsökning och nästa steg](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnostiska verktyg (endast för Windows-app)

Diagnostik-verktyg har mer avancerade diagnostiska verktyg för att hjälp du undersökningsapplikationen code problem, långsamhet, anslutningssträngar med mera. och proaktiv verktyg som hjälper dig att identifiera problem med CPU-användning, begäranden och minne.

### <a name="proactive-cpu-monitoring"></a>Proaktiv övervakning av CPU

Proaktiv CPU-övervakning ger dig ett enkelt, proaktiv sätt att vidta åtgärder när din app eller underordnad process för din app slukar stora processorresurser. Du kan ange dina egna CPU tröskelvärdet regler för att minska en hög CPU-problemet tillfälligt tills den verkliga orsaken för oväntade problem hittas.

![Proaktiv övervakning av CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>Proaktiv autoreparation

Proaktiv CPU-övervakning, erbjuder proaktiv autoreparation ett enkelt, proaktiv metod för att förhindra oväntade resultat av din app. Du kan ange dina egna regler baserat på antalet förfrågningar, bristfälliga, minnesgräns och HTTP-statuskod för att utlösa minskning åtgärder. Det här verktyget kan användas för att minska en oväntat beteende tillfälligt tills den verkliga orsaken till problemet finns. Mer information om proaktiv autoreparation finns [presenterar nya automatisk återställning upplevelse i app service-diagnostik](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Proaktiv autoreparation](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis-only-for-windows-app"></a>Ändra analysis (endast för Windows-app)

I en molntjänsternas utvecklingsmiljö vara det ibland svårt att hålla reda på alla ändringar som gjorts till din app och låta enbart pinpoint på en ändring som orsakas av defekta beteende. Ändra analys kan du begränsa på ändringar som görs i din app för att underlätta felsökning upplevelse. Ändra analys finns i **programändringar** och också inbäddad i en diagnostisk rapport som **programmet kraschar** så att du kan använda den samtidigt med andra mått.

Ändra analysis måste vara aktiverat innan du använder funktionen. Mer information om ändringen analys finns [Vi presenterar den nya ändring analysis-upplevelsen i App Service-diagnostik](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

![Ändra analysis-standardsida](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![Visa diff](./media/app-service-diagnostics/diff-view-12.png)