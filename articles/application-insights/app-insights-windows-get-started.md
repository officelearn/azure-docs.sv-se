<properties
    pageTitle="Analytics för Windows Phone- och Store-appar | Microsoft Azure"
    description="Analysera användningen och krascher i din Windows-enhetsapp."
    services="application-insights"
    documentationCenter="windows"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/26/2016"
    ms.author="awills"/>

# Analytics för Windows Phone- och Store-appar

Microsoft tillhandahåller två DevOps-lösningar för enheter: [HockeyApp](http://hockeyapp.net/) för analys på klientsidan och [Application Insights](app-insights-overview.md) för serversidan.

[HockeyApp](http://hockeyapp.net/) är vår mobila DevOps-lösning för appar på iOS-, OS X-, Android- eller Windows-enheter och för plattformsoberoende appar som baseras på Xamarin, Cordova och Unity. Med den här lösningen kan du distribuera versioner till betatestare, samla in kraschdata och få mätvärden om och feedback från användare. Lösningen är integrerad med Visual Studio Team Services och gör det enkelt att skapa distributioner och integreringar med arbetsobjekt. 

## Komma igång med HockeyApp

Gå till:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [HockeyApp för Windows](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)
* [HockeyApp-blogg](http://hockeyapp.net/blog/)
* Registrera dig för [Hockeyapp Preseason](http://hockeyapp.net/preseason/) och få tillgång till de senaste funktionerna och versionerna.

Om din app har en serversida använder du [Application Insights](app-insights-overview.md) för att övervaka webbserverdelen av din app på [ASP.NET](app-insights-asp-net.md) eller [J2EE](app-insights-java-get-started.md). 


Du kan också använda [Application Insights för Windows-skrivbordsappar](app-insights-windows-desktop.md).

## Analys, export och API-åtkomst till HockeyApp-data 

[Skapa en brygga HockeyApp-brygga](app-insights-hockeyapp-bridge-app.md) i Application Insights. Detta innebär att du kan:

* Använd det kraftfulla [Analytics](app-insights-analytics.md)-frågespråket via din telemetri. 
* [Exportera telemetri](app-insights-export-telemetry.md) till Azure Blob Storage.

## Nästa steg

* [Komma igång med HockeyApp för Windows](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)



<!--HONumber=sep16_HO1-->


