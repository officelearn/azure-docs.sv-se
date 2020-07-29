---
title: 'Application Insights: språk, plattformar och integreringar | Microsoft Docs'
description: Språk, plattformar och integreringar som är tillgängliga för Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 35dc6c5146edd13309a42702d1bc247333ff0fd7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322456"
---
# <a name="supported-languages"></a>Språk som stöds

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-get-started.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plattformar och ramverk som stöds

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentering för redan distribuerade program (kodad, agent-baserad)
* [Skalnings uppsättningar för virtuella Azure-datorer och virtuella Azure-datorer](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [ASP.NET – för appar som redan är live](./monitor-performance-live-website-now.md)
* [Azure Cloud Services](./cloudservices.md), inklusive både webb-och arbets roller
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Instrumentering genom kod (SDK: er)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../learn/mobile-center-quickstart.md) (App Center)
* [iOS](../learn/mobile-center-quickstart.md) (App Center)
* [Java EE](./java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [Universell Windows-app](../learn/mobile-center-quickstart.md) (App Center)
* [Windows-baserade skrivbordsprogram, tjänster och arbetarroller](./windows-desktop.md)

## <a name="logging-frameworks"></a>Ramverk för loggning
* [ILogger](./ilogger.md)
* [Log4Net, NLog eller System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J eller Logback](./java-trace-logs.md)
* [LogStash plugin](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Export och data analys
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>SDK:er som inte stöds
Vi är medvetna om att det finns flera andra Community-stödda SDK: er. Azure Monitor har dock bara stöd för att använda SDK: er som stöds på den här sidan. Vi kommer ständigt att utvärdera möjligheterna till att utöka vårt stöd för andra språk, så följ våra [GitHub-meddelanden](https://github.com/microsoft/ApplicationInsights-Announcements/issues) för att ta emot de senaste SDK-nyheterna. 

