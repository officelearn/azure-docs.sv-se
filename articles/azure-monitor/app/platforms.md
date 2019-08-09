---
title: 'Application Insights: språk, plattformar och integreringar | Microsoft Docs'
description: Språk, plattformar och integreringar som är tillgängliga för Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: ab573d1d9ca9d9db39135ea76e555bb0720d7b68
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68878781"
---
# <a name="supported-languages"></a>Språk som stöds

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.js](../../azure-monitor/app/nodejs.md)

## <a name="supported-platforms-and-frameworks"></a>Plattformar och ramverk som stöds

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentering för redan distribuerade program (kodad, agent-baserad)
* [Skalnings uppsättningar för virtuella Azure-datorer och virtuella Azure-datorer](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – för appar som redan är live](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md), inklusive både webb-och arbets roller
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentering genom kod (SDK: er)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java-EE](../../azure-monitor/app/java-get-started.md)
* [Node.js](https://www.npmjs.com/package/applicationinsights)
* [Universell Windows-app](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Windows-baserade skrivbordsprogram, tjänster och arbetarroller](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Ramverk för loggning
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog eller System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J eller Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash plugin](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Export och data analys
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>SDK: er som inte stöds
Vi är medvetna om att det finns flera andra Community-stödda SDK: er. Azure Monitor har dock bara stöd för att använda SDK: er som stöds på den här sidan. Vi kommer ständigt att utvärdera möjligheterna till att utöka vårt stöd för andra språk, så följ våra [GitHub-meddelanden](https://github.com/microsoft/ApplicationInsights-Announcements/issues) för att ta emot de senaste SDK-nyheterna. 
