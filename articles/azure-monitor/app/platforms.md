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
ms.date: 07/06/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: db79f2b1d3857aac2f71d2e18a3949f068b746eb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990059"
---
# <a name="languages---officially-supported-by-application-insights-team"></a>Språk – stöds officiellt av Application Insights-teamet

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript-webbsidor](../../azure-monitor/app/javascript.md)
* [Node.js](../../azure-monitor/app/nodejs.md)

## <a name="community-sdks"></a>Community-SDK: er

Det finns ett antal community-SDK: er för Azure Application Insights, många av dem som ursprungligen skapats av Microsoft. Community SDK: er hanteras inte officiellt av Microsoft. Vi kan inte tillhandahålla stöd för SDK: er som inte finns med i listan över officiellt stöd. Dessa SDK: er betraktas som experimentella och rekommenderas inte för produktions användning.

## <a name="platforms-and-frameworks"></a>Plattformar och ramverk
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
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
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
