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
ms.date: 09/01/2016
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 0aee18115f34c86e84054f1aeddcc99d563f5f64
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116525"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Utvecklaranalys: språk, plattformar och integreringar
Dessa objekt är implementeringar av [Application Insights](../../azure-monitor/app/app-insights-overview.md) som vi har hört talas om, inklusive vissa av tredje parter.

## <a name="languages---officially-supported-by-application-insights-team"></a>Språk – som officiellt stöds av Application Insights-teamet
* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript-webbsidor](../../azure-monitor/app/javascript.md)
* [Node.js](../../azure-monitor/app/nodejs.md)

## <a name="languages---community-supported"></a>Språk – som stöds av vårt community
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Annat](#projects)

## <a name="platforms-and-frameworks"></a>Plattformar och ramverk
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET – för appar som redan är live](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md) – inklusive både webb- och arbetarroller
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](../../azure-monitor/app/docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center)
* [J2EE](../../azure-monitor/app/java-get-started.md)
* [J2EE – för appar som redan är live](../../azure-monitor/app/java-live.md)
* [Node.js](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [SAFE Stack](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](https://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Universell Windows-app](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Windows-baserade skrivbordsprogram, tjänster och arbetarroller](../../azure-monitor/app/windows-desktop.md)
* [Annat](#projects)

## <a name="logging-frameworks"></a>Ramverk för loggning
* [Log4Net, NLog eller System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J eller Logback](../../azure-monitor/app/java-trace-logs.md)
* [Semantisk loggning (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) – integrerar med [Semantic Logging Application Block](https://msdn.microsoft.com/library/dn440729.aspx)
* [Molnbaserad belastningstestning](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [LogStash plugin](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)

## <a name="content-management-systems"></a>Innehållshanteringssystem
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](../../azure-monitor/app/sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Export- och dataanalys
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md )

## <a name="projects"></a> Skapa ditt eget SDK
Om det inte finns något SDK för ditt språk eller din plattform än kanske du vill skapa ett? Ta en titt på koden för befintliga SDK:er i [Application Insights SDK-projekt på GitHub](https://github.com/Microsoft/AppInsights-Home).
