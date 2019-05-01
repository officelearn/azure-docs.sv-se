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
ms.openlocfilehash: 5c01c2721a29bf142ee0ba53c9bc29ec66a7278f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727921"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilera live Azure Service Fabric-program med Application Insights

Du kan också distribuera Application Insights Profiler på de här tjänsterna:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurera distributionsdefinitionen miljö

Application Insights Profiler ingår Azure Diagnostics. Du kan installera Azure Diagnostics-tillägget med hjälp av en Azure Resource Manager-mall för Service Fabric-klustret. Hämta en [mall som installerar Azure Diagnostics på ett Service Fabric-kluster](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Om du vill konfigurera din miljö måste du vidta följande åtgärder:

1. Profiler har stöd för .NET Framework och .net Core. Om du använder .NET Framework, kontrollera att du använder [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare. Det räcker att bekräfta att det distribuerade Operativsystemet är `Windows Server 2012 R2` eller senare. Profiler har stöd för .NET Core 2.1 och nyare program.

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
  För Profiler att samla in profiler för dina begäranden spåra programmets åtgärder med Application Insights. För tillståndslösa API: er som du kan referera till anvisningar om hur [spåra begäranden Profileringen](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Läs mer om att spåra anpassade åtgärder i andra typer av appar, [spåra anpassade åtgärder med Application Insights SDK för .NET](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Distribuera programmet igen.


## <a name="next-steps"></a>Nästa steg

* Generera trafik till ditt program (till exempel starta en [tillgänglighetstestet](monitor-web-app-availability.md)). Vänta 10 till 15 minuter innan spårningarna starta som ska skickas till Application Insights-instans.
* Se [Profiler-spårningar](profiler-overview.md?toc=/azure/azure-monitor/toc.json) i Azure-portalen.
* Om du vill ha hjälp med felsökning av problem med Profiler finns i [Profiler felsökning](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
