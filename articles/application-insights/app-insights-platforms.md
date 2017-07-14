---
title: "Application Insights: språk, plattformar och integreringar | Microsoft Docs"
description: "Språk, plattformar och integreringar som är tillgängliga för Application Insights"
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 7f9b886259c065627055f62a3636ab65434ca57b
ms.contentlocale: sv-se
ms.lasthandoff: 05/23/2017


---
# Utvecklaranalys: språk, plattformar och integreringar
<a id="developer-analytics-languages-platforms-and-integrations" class="xliff"></a>
Dessa objekt är implementeringar av [Application Insights](app-insights-overview.md) som vi har hört talas om, inklusive vissa av tredje parter.

## Språk – som officiellt stöds av Application Insights-teamet
<a id="languages---officially-supported-by-application-insights-team" class="xliff"></a>
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [JavaScript-webbsidor](app-insights-javascript.md)

## Språk – som stöds av vårt community
<a id="languages---community-supported" class="xliff"></a>
* [Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Annat](#projects)

## Plattformar och ramverk
<a id="platforms-and-frameworks" class="xliff"></a>
* [Angular](https://www.npmjs.com/package/angular-applicationinsights)
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET – för appar som redan är live](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (HockeyApp)
* [Azure Web Apps](app-insights-azure-web-apps.md)
* [Azure Cloud Services](app-insights-cloudservices.md) – inklusive både webb- och arbetarroller
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (HockeyApp)
* [J2EE](app-insights-java-get-started.md)
* [J2EE – för appar som redan är live](app-insights-java-live.md)
* [Mac OS X app](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x) (HockeyApp)
* [Node.js](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Universell Windows-app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp) (HockeyApp)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Windows Phone 8 och 8.1-app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81) (HockeyApp)
* [Windows Presentation Foundation-app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps) (HockeyApp)
* [Windows-baserade skrivbordsprogram, tjänster och arbetarroller](app-insights-windows-desktop.md)
* [Annat](#projects)

## Ramverk för loggning
<a id="logging-frameworks" class="xliff"></a>
* [Log4Net, NLog eller System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java, Log4J eller Logback](app-insights-java-trace-logs.md)
* [Semantisk loggning (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) – integrerar med [Semantic Logging Application Block](https://msdn.microsoft.com/library/dn440729.aspx)
* [Molnbaserad belastningstestning](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [LogStash plugin](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [OMS Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)

## Innehållshanteringssystem
<a id="content-management-systems" class="xliff"></a>
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## Export- och dataanalys
<a id="export-and-data-analysis" class="xliff"></a>
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> Skapa ditt eget SDK
Om det inte finns något SDK för ditt språk eller din plattform än kanske du vill skapa ett? Ta en titt på koden för befintliga SDK:er i [Application Insights SDK-projekt på GitHub](https://github.com/Microsoft/AppInsights-Home).

