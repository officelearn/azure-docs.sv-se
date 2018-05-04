---
title: 'Application Insights: språk, plattformar och integreringar | Microsoft Docs'
description: Språk, plattformar och integreringar som är tillgängliga för Application Insights
services: application-insights
documentationcenter: ''
author: OlegAnaniev-MSFT
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mbullwin
ms.openlocfilehash: fdc75c69008d2f448d8a077a42bfccd24db6579a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Utvecklaranalys: språk, plattformar och integreringar
Dessa objekt är implementeringar av [Application Insights](app-insights-overview.md) som vi har hört talas om, inklusive vissa av tredje parter.

## <a name="languages---officially-supported-by-application-insights-team"></a>Språk – som officiellt stöds av Application Insights-teamet
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [JavaScript-webbsidor](app-insights-javascript.md)
* [Node.js](app-insights-nodejs.md)

## <a name="languages---community-supported"></a>Språk – som stöds av vårt community
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Annat](#projects)

## <a name="platforms-and-frameworks"></a>Plattformar och ramverk
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET – för appar som redan är live](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center, HockeyApp)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Azure Web Apps](app-insights-azure-web-apps.md)
* [Azure Cloud Services](app-insights-cloudservices.md) – inklusive både webb- och arbetarroller
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center, HockeyApp)
* [J2EE](app-insights-java-get-started.md)
* [J2EE – för appar som redan är live](app-insights-java-live.md)
* [macOS-appen](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x) (HockeyApp)
* [Node.js](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Universell Windows-app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp) (App Center, HockeyApp)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Windows Phone 8 och 8.1-app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81) (HockeyApp)
* [Windows Presentation Foundation-app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps) (HockeyApp)
* [Windows-baserade skrivbordsprogram, tjänster och arbetarroller](app-insights-windows-desktop.md)
* [Annat](#projects)

## <a name="logging-frameworks"></a>Ramverk för loggning
* [Log4Net, NLog eller System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java, Log4J eller Logback](app-insights-java-trace-logs.md)
* [Semantisk loggning (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) – integrerar med [Semantic Logging Application Block](https://msdn.microsoft.com/library/dn440729.aspx)
* [Molnbaserad belastningstestning](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [LogStash plugin](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)

## <a name="content-management-systems"></a>Innehållshanteringssystem
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Export- och dataanalys
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> Skapa ditt eget SDK
Om det inte finns något SDK för ditt språk eller din plattform än kanske du vill skapa ett? Ta en titt på koden för befintliga SDK:er i [Application Insights SDK-projekt på GitHub](https://github.com/Microsoft/AppInsights-Home).
