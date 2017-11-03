---
title: "Azure Application Insights stöd för flera komponenter, mikrotjänster och behållare | Microsoft Docs"
description: "Övervakning av appar som består av flera komponenter eller roller för prestanda och användning."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 046661bf7903b4e5ea528282ad5170901a45b35c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Övervaka flera komponenten program med Application Insights (förhandsversion)

Du kan övervaka appar som består av flera server-komponenter, roller eller -tjänster med [Azure Application Insights](app-insights-overview.md). Hälsotillståndet för komponenterna och relationer mellan dem visas på en enda programavbildningen. Du kan spåra enskilda åtgärder med hjälp av flera komponenter med automatisk HTTP korrelation. Behållaren diagnostik integrering och samverkar med programtelemetri. Använda en enda Application Insights-resurs för alla komponenter i ditt program. 

![Flera komponenten programavbildningen](./media/app-insights-monitor-multi-role-apps/app-map.png)

Vi använder här ”komponent” betyder någon fungerande del av ett omfattande program. Till exempel en typisk affärsprogram kan bestå av klientkod som körs i webbläsare som kommunicerar med en eller flera web app-tjänster, som i sin tur använder tillbaka avsluta tjänster. Server-komponenter kan vara lokalt på i molnet, eller kanske Azure webb- och arbetsroller roller eller kan köras i behållare, till exempel Docker eller Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Dela en enda Application Insights-resurs 

Med viktiga tekniken är att skicka telemetri från varje komponent i ditt program till samma Application Insights-resurs, men använda den `cloud_RoleName` egenskapen att skilja komponenter vid behov. Application Insights SDK lägger till den `cloud_RoleName` egenskapen telemetri komponenter genererar. Till exempel SDK kommer att lägga till en webbplatsens namn eller tjänstnamnet som rollen ska den `cloud_RoleName` egenskapen. Du kan åsidosätta detta värde med en telemetryinitializer. Kartan program använder den `cloud_RoleName` egenskapen för att identifiera komponenterna på kartan.

Mer information om hur Åsidosätt den `cloud_RoleName` egenskapen Se [lägga till egenskaper: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

I vissa fall kan detta kanske inte är rätt och du kanske föredrar att använda separata resurser för olika grupper av komponenter. Du kan behöva använda olika resurser för hantering eller fakturering. Med hjälp av separata resurser innebär att du inte ser alla komponenter som visas på en enda programavbildningen; och att du kan inte fråga på komponenter i [Analytics](app-insights-analytics.md). Du måste ange olika resurser.

Med den begränsning antar vi i resten av det här dokumentet som du vill skicka data från flera komponenter till en Application Insights-resurs.

## <a name="configure-multi-component-applications"></a>Konfigurera flera komponenten program

För att få flera komponenten programavbildningen måste att uppnå dessa mål:

* **Installera den senaste förhandsversionen** Application Insights-paketet i varje komponent i programmet. 
* **Dela en enda Application Insights-resurs** för alla komponenter i ditt program.
* **Aktivera flera rollen programavbildningen** i bladet förhandsgranskningar.

Konfigurera varje komponent i ditt program med en lämplig metod för typen. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Installera det senaste förhandsversionen paketet

Uppdatera eller installera Application Insights-paket i projektet för varje serverkomponent. Om du använder Visual Studio:

1. Högerklicka på projektet och välj **hantera NuGet-paket**. 
2. Välj **inkludera förhandsversion**.
3. Om Application Insights visas paketen i uppdateringar, markera dem. 

    Annars kan söka efter och installera lämplig paketet:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - komponenter som körs som gäst körbara filer och Docker-behållare som kör ett i Service Fabric-program
    * Microsoft.ApplicationInsights.ServiceFabric.Native - för tillförlitlig tjänster i ServiceFabric program
    * Microsoft.ApplicationInsights.Kubernetes för komponenter som körs i Docker på Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Dela en enda Application Insights-resurs

* Högerklicka på ett projekt i Visual Studio och välj **konfigurera Application Insights**, eller **Programinsikter > Konfigurera**. Använd guiden Skapa Application Insights-resurs för det första projektet. Välj samma resurs för efterföljande projekt.
* Om det finns inga Application Insights-menyn, konfigurera manuellt:

   1. I [Azure-portalen](https://portal,azure.com), öppna Application Insights-resurs som du redan har skapats för en annan komponent.
   2. I bladet översikt, öppna Essentials nedrullningsbara fliken och kopiera den **Instrumentation nyckel.**
   3. Öppna ApplicationInsights.config och infoga i projektet:`<InstrumentationKey>your copied key</InstrumentationKey>`

![Kopiera nyckeln instrumentation till .config-filen](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Aktivera flera rollen programavbildningen

Öppna resursen för ditt program i Azure-portalen. I bladet förhandsgranskningar aktivera *flera rollen programavbildningen*.

### <a name="4-enable-docker-metrics-optional"></a>4. Aktivera Docker mätvärden (valfritt) 

Om en komponent som körs i en Docker som finns på en Windows Azure-dator, kan du samla in ytterligare mått från behållaren. Infoga det i ditt [Azure diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md) konfigurationsfil:

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

## <a name="use-cloudrolename-to-separate-components"></a>Använd cloud_RoleName för att avgränsa komponenter

Den `cloud_RoleName` bifogas till all telemetri. Komponenten - roll eller tjänst - kommer telemetrin identifieras. (Det är inte samma som cloud_RoleInstance som separerar identiska roller som körs parallellt på flera serverprocesser eller datorer.)

Du kan filtrera eller segmentera dina telemetri som använder den här egenskapen i portalen. Fel-bladet är filtrerad och visar bara information från tjänsten frontwebbservern filtrera fel från CRM API-serverdel i det här exemplet:

![Mått diagram åtskilda med rollen Molnnamn](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Spåra åtgärder mellan komponenter

Du kan spåra från en komponent till en annan, anrop under bearbetning av en enskild åtgärd.


![Visa telemetri för åtgärden](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Klicka här för att en korrelerade lista över telemetri för den här åtgärden på frontwebbservern och backend-API:

![Söka i komponenter](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Nästa steg

* [Separata telemetri från utveckling, Test och produktion](app-insights-separate-resources.md)
