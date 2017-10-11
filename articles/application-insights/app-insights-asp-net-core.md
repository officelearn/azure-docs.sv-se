---
title: "Azure Application Insights för ASP.NET Core | Microsoft Docs"
description: "Övervaka webbprogram för tillgänglighet, prestanda och användning."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: d86495eea467977f6c079de72e2b49a2a1da2b60
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights för ASP.NET Core
[Application Insights](app-insights-overview.md) kan du övervaka ditt webbprogram för tillgänglighet, prestanda och användning. Med den feedback du får om appens prestanda och effektivitet kan du fatta välgrundade beslut om designen i varje utvecklingslivscykel.

![Exempel](./media/app-insights-asp-net-core/sample.png)

Du behöver en prenumeration med [Microsoft Azure](http://azure.com). Logga in med ett Microsoft-konto som du kanske har för Windows, XBox Live eller andra Microsoft-molntjänster. Gruppen kan ha en organisationens prenumeration på Azure: Be ägare att lägga till dig i den med ditt Microsoft-konto.

## <a name="getting-started"></a>Komma igång

* Högerklicka på ditt projekt i Visual Studio Solution Explorer och markera **konfigurera Application Insights**, eller **Lägg till > Application Insights**. [Läs mer](app-insights-asp-net.md).
* Om du inte ser dessa kommandon på menyn, följer du de [manuell komma igång-guide](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started). Du kan behöva göra detta om projektet har skapats med en version av Visual Studio innan 2017.

## <a name="using-application-insights"></a>Använda Application Insights
Logga in på den [Microsoft Azure-portalen](https://portal.azure.com)väljer **alla resurser** eller **Programinsikter**, och välj sedan den resurs du skapat för att övervaka din app.

Använda din app på ett tag i en separat webbläsarfönster. Data som visas i Application Insights-diagram visas. (Du kan behöva klicka på Uppdatera.) Det är bara en liten mängd data medan du utvecklar, men dessa diagram verkligen kan underlätta alive när du publicerar din app och har många användare. 

På översiktssidan visar viktiga prestandadiagram: serversvarstid, sidinläsningstiden och antalet misslyckade begäranden. Klicka på diagram om du vill se mer diagram och data.

Vyer i portalen delas in i tre huvudkategorier:

* [Metrics Explorer](app-insights-metrics-explorer.md) visar diagram och tabeller för mått och antal, till exempel svarstider, fel eller mått som du skapar själv med de [API](app-insights-api-custom-events-metrics.md). Filtrera och segmentera data av egenskapsvärden för att få en bättre förståelse för din app och dess användare.
* [Sök Explorer](app-insights-diagnostic-search.md) visar enskilda händelser, till exempel specifika begäranden, undantag, loggspårningar eller händelser som du själv har skapat med den [API](app-insights-api-custom-events-metrics.md). Filtrera och söka i händelserna och navigera mellan relaterade händelser att undersöka problem.
* [Analytics](app-insights-analytics.md) kan du köra SQL-liknande frågor via din telemetri och är ett kraftfullt verktyg för analys och diagnostik.

## <a name="alerts"></a>Aviseringar
* Automatiskt få [proaktiv diagnostiska aviseringar](app-insights-proactive-diagnostics.md) som berättar om avvikande ändringar i fel och andra mått.
* Ställ in [tillgänglighetstester](app-insights-monitor-web-app-availability.md) att testa din webbplats kontinuerligt från platser över hela världen och få e-postmeddelanden när någon testa misslyckas.
* Ställ in [mått aviseringar](app-insights-monitor-web-app-availability.md) du behöver veta om mått som svarstider eller undantag priser går utanför acceptabla gränser.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="open-source"></a>Öppen källkod
[Läsa och bidra till koden](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


## <a name="next-steps"></a>Nästa steg
* [Lägg till telemetri till dina webbsidor](app-insights-javascript.md) övervakaren sidan användning och prestanda.
* [Övervaka beroenden](app-insights-asp-net-dependencies.md) att se om REST, SQL eller andra externa resurser saktar ner dig.
* [Använd API](app-insights-api-custom-events-metrics.md) att skicka dina egna händelser och mått för en mer detaljerad vy av appens prestanda och användning.
* [Tillgänglighetstester](app-insights-monitor-web-app-availability.md) Kontrollera din app hela tiden från hela världen. 

