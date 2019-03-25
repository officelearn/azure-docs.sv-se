---
title: Profilera live Azure Service Fabric-program med Application Insights | Microsoft Docs
description: Aktivera Profiler för ett Service Fabric-program
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 0b1a06d181fc4d2a44d389d47d1f9480c2fdcb40
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401092"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilera live Azure Service Fabric-program med Application Insights

Du kan också distribuera Application Insights Profiler på de här tjänsterna:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurera distributionsdefinitionen miljö

Application Insights Profiler ingår Azure Diagnostics. Du kan installera Azure Diagnostics-tillägget med hjälp av en Azure Resource Manager-mall för Service Fabric-klustret. Hämta en [mall som installerar Azure Diagnostics på ett Service Fabric-kluster](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Om du vill konfigurera din miljö måste du vidta följande åtgärder:

1. Så att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare, så är tillräckliga för att bekräfta att det distribuerade Operativsystemet är `Windows Server 2012 R2` eller senare.

1. Sök efter den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) tillägget i mallfilen distribution.

1. Lägg till följande `SinksConfig` avsnittet som ett underordnat element av `WadCfg`. Ersätt den `ApplicationInsightsProfiler` egenskapsvärdet med din egen Application Insights-instrumenteringsnyckeln:  

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

1. Distribuera Service Fabric-klustret med hjälp av Azure Resource Manager-mall.  
  Om inställningarna är korrekta, Application Insights Profiler installeras och aktiveras när Azure Diagnostics-tillägget installeras. 

1. Lägg till Application Insights till ditt Service Fabric-program.  
  För Profiler att samla in profiler för dina begäranden spåra programmets åtgärder med Application Insights. För tillståndslösa API: er som du kan referera till anvisningar om hur [spåra begäranden Profileringen](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Mer information om att spåra anpassade åtgärder i andra typer av appar i [spåra anpassade åtgärder med Application Insights SDK för .NET](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Distribuera programmet igen.

> [TIPS] För virtuella datorer är ett alternativ till ovanstående JSON-baserade är att navigera i Azure portal för att **virtuella datorer** > **diagnostikinställningar** > **Egenskaperna** > **Set skicka diagnostiska data till Application Insights aktiverad** och välj sedan en Application Insights-konto eller en specifik ikey.

## <a name="next-steps"></a>Nästa steg

* Generera trafik till ditt program (till exempel starta en [tillgänglighetstestet](monitor-web-app-availability.md)). Vänta 10 till 15 minuter innan spårningarna starta som ska skickas till Application Insights-instans.
* Se [Profiler-spårningar](profiler-overview.md?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
* Om du vill ha hjälp med felsökning av problem med Profiler finns i [Profiler felsökning](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
