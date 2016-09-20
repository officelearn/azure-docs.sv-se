<properties
    pageTitle="Analytics öor Android-appar | Microsoft Azure"
    description="Analysera användning och prestanda i din Android-app."
    services="application-insights"
    documentationCenter="android"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/26/2016"
    ms.author="awills"/>

# Analytics för Android-appar

Microsoft tillhandahåller två DevOps-lösningar för enheter: [HockeyApp](http://hockeyapp.net/) för analys på klientsidan och [Application Insights](app-insights-overview.md) för serversidan.

[HockeyApp](http://hockeyapp.net/) är vår mobila DevOps-lösning för appar på iOS-, OS X-, Android- eller Windows-enheter och för plattformsoberoende appar som baseras på Xamarin, Cordova och Unity. Med den här lösningen kan du distribuera versioner till betatestare, samla in kraschdata och få feedback från användare. Lösningen är integrerad med Visual Studio Team Services och gör det enkelt att skapa distributioner och integreringar med arbetsobjekt.

Gå till:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [Komma igång med HockeyApp för Android](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk).
* [HockeyApp-blogg](http://hockeyapp.net/blog/)
* Registrera dig för [Hockeyapp Preseason](http://hockeyapp.net/preseason/) och få tillgång till de senaste funktionerna och versionerna.

Om din app har en serversida använder du [Application Insights](app-insights-overview.md) för att övervaka webbserverdelen av din app på [ASP.NET](app-insights-asp-net.md) eller [J2EE](app-insights-java-get-started.md). 


## Analys, export och API-åtkomst till HockeyApp-data 

[Skapa en brygga HockeyApp-brygga](app-insights-hockeyapp-bridge-app.md) i Application Insights. Detta innebär att du kan:

* Använd det kraftfulla [Analytics](app-insights-analytics.md)-frågespråket via din telemetri. 
* [Exportera telemetri](app-insights-export-telemetry.md) till Azure Blob Storage.

## Nästa steg

* [Komma igång med HockeyApp för Android](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)




<!--HONumber=sep16_HO1-->


