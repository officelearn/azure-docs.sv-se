---
title: Azure Application Insights telemetri datamodell - begäran telemetri | Microsoft Docs
description: Application Insights datamodellen för begärandetelemetri
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: e0bdaf132474d8e5eaac6a9c65093d27d673d343
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="request-telemetry-application-insights-data-model"></a>Begära telemetri: Application Insights-datamodell

En begäran telemetri objekt (i [Programinsikter](app-insights-overview.md)) representerar logiska sekvensen av körningen som utlöses av en extern begäran till programmet. Varje begäran körs identifieras med unikt `ID` och `url` som innehåller parametrarna för körning. Du kan gruppera begäranden efter logiska `name` och definiera de `source` för den här begäran. Kodkörning kan resultera i `success` eller `fail` och har en viss `duration`. Både lyckade och misslyckade körningar kan grupperas ytterligare med `resultCode`. Starttid för begärandetelemetri som definierats på kuvert-nivå.

Begäran telemetri stöder standard utökningsbarhet modellen med anpassade `properties` och `measurements`.

## <a name="name"></a>Namn

Namnet på begäran representerar kodsökvägen vid bearbetning av begäran. Låg kardinalitet värdet för att tillåta bättre gruppering av begäranden. För HTTP-begäran som den representerar den HTTP-metoden och mallen för URL-sökväg som `GET /values/{id}` utan den faktiska `id` värde.

Application Insights web SDK skickar begäran namn ”i befintligt skick” med avseende på små bokstäver. Gruppering på Användargränssnittet är skiftlägeskänsligt så `GET /Home/Index` räknas separat från `GET /home/INDEX` även om ofta de resulterar i samma körning av kontrollanten och åtgärden. Det för som beror på att URL: er i allmänhet är [skiftlägeskänsliga](http://www.w3.org/TR/WD-html40-970708/htmlweb.html). Du kanske vill se om alla `404` inträffade för URL: er som har skrivit in versaler. Du kan läsa mer på begäran namnsamling av ASP.Net Web SDK i den [blogginlägget](http://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maxlängd: 1024 tecken

## <a name="id"></a>ID

Identifierare för en instans för anrop av begäran. Används för korrelation mellan begäran och andra telemetri-objekt. ID ska vara globalt unika. Mer information finns i [korrelation](application-insights-correlation.md) sidan.

Maxlängd: 128 tecken

## <a name="url"></a>URL

URL-begäran med alla frågan string-parametrar.

Maxlängd: 2048 tecken

## <a name="source"></a>Källa

Källan för begäran. Exempel är nyckeln instrumentation anroparens eller ip-adressen för anroparen. Mer information finns i [korrelation](application-insights-correlation.md) sidan.

Maxlängd: 1024 tecken

## <a name="duration"></a>Varaktighet

Begär tid i formatet: `DD.HH:MM:SS.MMMMMM`. Måste vara positiv och mindre än `1000` dagar. Det här fältet är obligatoriskt eftersom begärandetelemetri motsvarar igen med början och slutet.

## <a name="response-code"></a>Svarskod

Resultatet av en begäran körs. HTTP-statuskoden för HTTP-begäranden. Det kan vara `HRESULT` värde eller ett undantag av typen för andra typer av begäranden.

Maxlängd: 1024 tecken

## <a name="success"></a>Lyckades

Uppgift om lyckade och misslyckade anrop. Det här fältet är obligatoriskt. Ange när inte uttryckligen till `false` -begäran anses vara ska lyckas. Det här värdet till `false` om åtgärden avbröts av undantagsfel eller returnerade felkoden resultat.

För webbprogram, definiera Application Insights begäran som misslyckades när svarskoden är mindre den `400` eller lika med `401`. Det finns dock fall när standardmappningen inte matchar den semantiska av programmet. Svarskoden `404` kan tyda på ”inga poster”, som kan vara en del av regelbundet flöde. Det kan också indikera en bruten länk. Du kan även implementera mer avancerad logik för brutna länkar. Du kan markera brutna länkar som fel endast när dessa länkar befinner sig på samma plats genom att analysera url referent. Eller markera dem som fel vid åtkomst från företagets mobila program. På liknande sätt `301` och `302` anger ett fel vid åtkomst från klienten som stöder omdirigering.

Delvis accepteras innehåll `206` kan tyda på ett fel på en övergripande begäran. Application Insights slutpunkten får exempelvis en batch med telemetri artiklar som en enskild begäran. Den returnerar `206` när vissa objekt i batchen inte bearbetades. Öka mängden `206` tyder på problem som ska undersökas. Liknande logik som gäller för `207` flera Status där lyckas kanske sämsta av separata svarskoder.

Du kan läsa mer på begäran resultatet kod och statuskod i den [blogginlägget](http://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- [Skriva anpassade begärandetelemetri](app-insights-api-custom-events-metrics.md#trackrequest)
- Se [datamodellen](application-insights-data-model.md) för Application Insights typer och modell.
- Lär dig hur du [konfigurerar ASP.NET Core](app-insights-asp-net.md) program med Application Insights.
- Checka ut [plattformar](app-insights-platforms.md) stöds av Application Insights.
