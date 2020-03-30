---
title: Profil live Azure Service Fabric-appar med Application Insights
description: Aktivera Profiler för ett Service Fabric-program
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 664d6eb377185613a1a5670daf6747b482c79d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671621"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profil live Azure Service Fabric-program med Application Insights

Du kan också distribuera Application Insights Profiler på dessa tjänster:
* [Azure App-tjänst](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Virtuella Azure-datorer](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurera miljödistributionsdefinitionen

Application Insights Profiler ingår i Azure Diagnostics. Du kan installera Azure Diagnostics-tillägget med hjälp av en Azure Resource Manager-mall för ditt Service Fabric-kluster. Hämta en [mall som installerar Azure Diagnostics på ett Service Fabric Cluster](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Så här konfigurerar du miljön:

1. Profiler stöder .NET Framework och .Net Core. Om du använder .NET Framework kontrollerar du att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare. Det räcker att bekräfta att det `Windows Server 2012 R2` distribuerade operativsystemet är eller senare. Profiler stöder .NET Core 2.1 och nyare program.

1. Sök efter [Azure Diagnostics-tillägget](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) i distributionsmallfilen.

1. Lägg till `SinksConfig` följande avsnitt som `WadCfg`ett underordnat element i . Ersätt `ApplicationInsightsProfiler` egenskapsvärdet med din egen instrumenteringsnyckel för Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Information om hur du lägger till diagnostiktillägget i distributionsmallen finns i [Använda övervakning och diagnostik med mallar för Windows VM och Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Distribuera ditt Service Fabric-kluster med hjälp av din Azure Resource Manager-mall.  
  Om dina inställningar är korrekta installeras och aktiveras Application Insights Profiler när Azure Diagnostics-tillägget installeras. 

1. Lägg till programinsikter i ditt Service Fabric-program.  
  För att Profiler ska kunna samla in profiler för dina begäranden måste ditt program spåra åtgärder med Application Insights. För tillståndslösa API:er kan du läsa instruktioner för [spårning av begäranden om profilering](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Mer information om hur du spårar anpassade åtgärder i andra typer av appar finns i [spåra anpassade åtgärder med Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Distribuera om programmet.


## <a name="next-steps"></a>Nästa steg

* Generera trafik till ditt program (till exempel starta ett [tillgänglighetstest).](monitor-web-app-availability.md) Vänta sedan 10 till 15 minuter på att spårningar ska börja skickas till application insights-instansen.
* Se [Profiler-spårningar](profiler-overview.md?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
* Mer information om felsökning av Profiler-problem finns i [Felsökning av Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
