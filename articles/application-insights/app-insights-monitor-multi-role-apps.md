---
title: Azure Application Insights-stöd för flera komponenter, mikrotjänster och behållare | Microsoft Docs
description: Övervaka appar som består av flera komponenter eller roller för prestanda och användning.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: mbullwin
ms.openlocfilehash: bf247748415822d5ba1a0e652fdeff384d8e8db1
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982059"
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Övervaka program med flera komponenter med Application Insights (förhandsversion)

Du kan övervaka appar som består av flera komponenter, roller eller tjänster med [Azure Application Insights](app-insights-overview.md). Hälsotillståndet för komponenterna och relationer mellan dem visas på en enda programavbildning. Du kan spåra enskilda åtgärder via flera komponenter med automatisk HTTP korrelation. Behållardiagnostik integrering och med programtelemetri. Använda en enda Application Insights-resurs för alla komponenter i ditt program. 

![Flera komponenter programavbildning](./media/app-insights-monitor-multi-role-apps/application-map-001.png)

Vi använder här ”component” avses någon fungerande del av en stor tillämpning. Till exempel ett typiskt affärsprogram kan bestå av klientkod som körs i webbläsare, pratar till en eller flera web app services, som i sin tur använda tillbaka sluta tjänster. Server-komponenter kan vara hanteras lokalt på i molnet, eller kan vara Azure webb- och worker-roller eller kan köras i behållare, till exempel Docker eller Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Dela en enda Application Insights-resurs 

Viktiga tekniken här innebär att skicka telemetri från alla komponenter i din app för samma Application Insights-resursen, men använda den `cloud_RoleName` egenskapen att skilja mellan komponenter vid behov. Application Insights SDK lägger till den `cloud_RoleName` egenskapen telemetri-komponenter genererar. Till exempel SDK kommer att lägga till en webbplatsens namn eller tjänstnamnet som rollen ska den `cloud_RoleName` egenskapen. Du kan åsidosätta det här värdet med en telemetryinitializer. Programkartan använder den `cloud_RoleName` egenskapen att identifiera komponenterna på kartan.

För mer information om hur åsidosätter den `cloud_RoleName` egenskapen Se [Lägg till egenskaper: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

I vissa fall kan det kanske inte är lämplig och kanske du föredrar att använda separata resurser för olika grupper av komponenter. Du kan behöva använda olika resurser för hantering och fakturering.

Med den teamindela förutsätter vi i resten av det här dokumentet som du vill skicka data från flera komponenter till en Application Insights-resurs.

## <a name="configure-multi-component-applications"></a>Konfigurera program med flera komponenter

Om du vill ha flera komponenter programavbildning behöver att åstadkomma detta:

* **Installera den senaste förhandsversionen** Application Insights-paketet i varje komponent i programmet. 
* **Dela en enda Application Insights-resurs** för alla komponenter i ditt program.
* **Aktivera sammansatt Programkarta** på bladet förhandsgranskningar.

Konfigurera varje komponent i ditt program med en lämplig metod för typen. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Installera det senaste förhandsversionen-paketet

Uppdatera eller installera Application Insights-paket i projektet för varje serverkomponent. Om du använder Visual Studio:

1. Högerklicka på ett projekt och välj **hantera NuGet-paket**. 
2. Välj **inkludera förhandsversion**.
3. Om Application Insights-paket visas i uppdateringar kan du välja dem. 

    Annars kan söka efter och installera lämpligt paket:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - för komponenter som körs som körbara gäster och Docker-behållare som kör ett i Service Fabric-program
    * Microsoft.ApplicationInsights.ServiceFabric.Native - för reliable services i Service fabric-program
    * Microsoft.ApplicationInsights.Kubernetes för komponenter som körs i Docker på Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Dela en enda Application Insights-resurs

* Högerklicka på ett projekt i Visual Studio och välj **konfigurera Application Insights**, eller **Application Insights > Konfigurera**. Använd guiden för det första projektet är för att skapa en Application Insights-resurs. För efterföljande projekt, väljer du samma resurs.
* Om det finns inga Application Insights-menyn, konfigurerar du manuellt:

   1. I [Azure-portalen](https://portal,azure.com), öppna Application Insights-resurs som du redan har skapats för en annan komponent.
   2. I fliken översiktsbladet, öppna Essentials listrutan och kopiera den **Instrumenteringsnyckeln.**
   3. Öppna ApplicationInsights.config och infoga i projektet: `<InstrumentationKey>your copied key</InstrumentationKey>`

![Kopiera instrumenteringsnyckeln till .config-filen](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-composite-application-map"></a>3. Aktivera sammansatt Programkarta

Öppna resursen för ditt program i Azure-portalen. Under rubriken konfigurera underordnade, klickar du på förhandsversioner för att öppna bladet förhandsversioner. På bladet förhandsgranskningar aktivera *sammansatt Programkarta*.

### <a name="4-enable-docker-metrics-optional"></a>4. Aktivera mätvärden för Docker (valfritt) 

Om en komponent som körs i Docker finns på en Windows Azure-dator kan du samla in ytterligare mått från behållaren. Infoga detta i din [Azure-diagnostik](../monitoring-and-diagnostics/azure-diagnostics.md) konfigurationsfil:

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-components"></a>Använd cloud_RoleName mellan komponenter

Den `cloud_RoleName` bifogas till all telemetri. Den identifierar den komponent - roll eller tjänst - som samlas in telemetri. (Det är inte samma som cloud_RoleInstance som avgränsar identiska roller som körs parallellt på flera serverprocesser eller datorer.)

Du kan filtrera eller dela din telemetri med hjälp av den här egenskapen i portalen. I det här exemplet filtreras bladet fel för att visa bara information från tjänsten frontwebbservern filtrering ut fel från CRM-API-serverdelen:

![Måttdiagram uppdelat efter Molnrollnamn](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Spåra åtgärder mellan komponenter

Du kan spåra från en komponent till en annan, anrop som görs vid bearbetning av en enskild åtgärd.


![Visa telemetri för åtgärden](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Klicka vidare till en korrelerad telemetri för den här åtgärden-lista över frontwebbservern och serverdels-API:

![Sök bland komponenter](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Nästa steg

* [Separata telemetri från utveckling, testning och produktion](app-insights-separate-resources.md)
