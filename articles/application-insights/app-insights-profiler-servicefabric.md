---
title: Profilera live Azure Service Fabric-program med Application Insights | Microsoft Docs
description: Aktivera profiler för ett Service Fabric-program
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 071d1acb592220f7da83a47fd7dffa757ef81a95
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142577"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilera live Azure Service Fabric-program med Application Insights

Du kan också distribuera Application Insights profiler på de här tjänsterna:
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>Konfigurera distributionsdefinitionen miljö

Application Insights Profiler tas med i Windows Azure Diagnostics SÄKERHETSSPECIFIKA. WAD-tillägget kan installeras med hjälp av en mall i Azure RM för Service Fabric-klustret. Det finns en exempelmall: [ **mall som installerar WAD på ett Service Fabric-kluster.**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Om du vill konfigurera din miljö måste du vidta följande åtgärder:
1. Så att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare, så är tillräckliga för att bekräfta att det distribuerade Operativsystemet är `Windows Server 2012 R2` eller senare.

1. Sök efter den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) tillägget på distributionsmallen för filen och Lägg sedan till följande `SinksConfig` avsnittet som ett underordnat element av `WadCfg`. Ersätt den `ApplicationInsightsProfiler` egenskapsvärdet med din egen Application Insights-instrumenteringsnyckeln:  

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

      Information om att lägga till Diagnostics-tillägg till mallen för distribution finns i [Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager-mallar](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
1. Distribuera Service Fabric-klustret med hjälp av Azure Resource Manager-mall. Om inställningarna är korrekta, Application Insights Profiler installeras och aktiveras när WAD tillägget installeras. 
1. Lägg till Application Insights till ditt Service Fabric-program. Ditt program måste skickar om begärandedata till application insights för profiler om du vill samla in profiler för dina begäranden. Du hittar anvisningar [här.](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)
1. Distribuera programmet igen.

> [TIPS] För virtuella datorer är ett alternativ till ovanstående json-baserade att navigera i Azure portal för att **virtuella datorer** > **diagnostikinställningar** >  **Egenskaperna** > Set skicka diagnostiska data till Application Insights till **aktiverad** och välj antingen ett Application Insights-konto eller en specifik ikey.

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program (till exempel starta en [tillgänglighetstestet](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Vänta 10 till 15 minuter innan spårningarna starta som ska skickas till Application Insights-instans.
- Se [Profiler-spårningar](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
- Få hjälp med felsökning av problem med profiler i [Profiler felsökning](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).