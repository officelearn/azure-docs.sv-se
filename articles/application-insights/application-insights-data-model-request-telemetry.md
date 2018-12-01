---
title: Modell för telemetridata för Azure Application Insights - begäran telemetri | Microsoft Docs
description: Application Insights-datamodell för begärandetelemetri
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: d3a2fe9b51e0e6e9de653abb1bfa56a2529218cb
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722474"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetri för begäran: Application Insights-datamodell

Ett begärandetelemetriobjekt (i [Application Insights](app-insights-overview.md)) representerar den logiska ordningsföljden körning som utlöses av en extern begäran till programmet. Varje begäran-körning identifieras med unikt `ID` och `url` som innehåller parametrarna för körning. Du kan gruppera begäranden efter logiska `name` och definiera den `source` för den här förfrågan. Fjärrkörning av kod kan medföra `success` eller `fail` och har en viss `duration`. Både lyckade och misslyckade körningar kan grupperas ytterligare med `resultCode`. Starttid för begärandetelemetri som definierats för kuvert-nivå.

Begäran om telemetri stöder standard utökningsmodellen med hjälp av anpassade `properties` och `measurements`.

## <a name="name"></a>Namn

Namnet på begäran representerar kodsökvägar vid bearbetning av begäran. Kardinalitet för lågt värde så att bättre gruppering av begäranden. För HTTP-förfrågningar som den representerar den HTTP-metoden och URL-sökväg-mallen som `GET /values/{id}` utan den faktiska `id` värde.

Application Insights web SDK skickar namnet på begäran ”i befintligt skick” för små bokstäver. Gruppering på Användargränssnittet är skiftlägeskänsligt så `GET /Home/Index` räknas separat från `GET /home/INDEX` även om de leder ofta samma kontrollanten och åtgärden körningen. Orsaken till som är att URL: er i allmänhet är [skiftlägeskänsliga](http://www.w3.org/TR/WD-html40-970708/htmlweb.html). Du kanske vill se om alla `404` inträffade för URL: er som skrivits i versaler. Du kan läsa mer på begäran namnsamling av ASP.Net Web SDK i den [blogginlägget](http://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maxlängd: 1024 tecken

## <a name="id"></a>ID

Identifierare för en instans för anrop av begäran. Används för korrelation mellan begäran och andra objekt som telemetri. ID: T måste vara globalt unikt. Mer information finns i [korrelation](application-insights-correlation.md) sidan.

Maxlängd: 128 tecken

## <a name="url"></a>URL

URL för begäran med alla parametrar för frågesträngen.

Maxlängd: 2048 tecken

## <a name="source"></a>Källa

Källan för begäran. Exempel är instrumenteringsnyckeln för anroparen eller ip-adressen för anroparen. Mer information finns i [korrelation](application-insights-correlation.md) sidan.

Maxlängd: 1024 tecken

## <a name="duration"></a>Varaktighet

Varaktighet i format för begäran: `DD.HH:MM:SS.MMMMMM`. Måste vara positivt och mindre än `1000` dagar. Det här fältet är obligatoriskt eftersom begärandetelemetri representerar igen med början och slutet.

## <a name="response-code"></a>Svarskod

Resultatet av en Frågekörningen. HTTP-statuskod för HTTP-begäranden. Det kan vara `HRESULT` värde eller ett undantag typ för andra typer av begäranden.

Maxlängd: 1024 tecken

## <a name="success"></a>Lyckades

Uppgift om lyckade och misslyckade anrop. Det här fältet är obligatoriskt. Ange när inte uttryckligen till `false` -begäran anses vara ska lyckas. Det här värdet till `false` om åtgärden avbröts av undantag eller felkoden resultat returnerades.

För webbprogram, Application Insights kan du definiera begäran som misslyckad när svarskoden är mindre den `400` eller lika med `401`. Det finns dock fall när den här standardmappningen inte matchar den semantiska av programmet. Svarskod `404` kan tyda på ”inga poster”, vilket kan vara en del av regelbundna flöde. Det kan också indikera en bruten länk. Du kan även implementera mer avancerad logik för brutna länkar. Du kan markera brutna länkar som fel endast när länkarna finns på samma plats genom att analysera url referent. Eller markera dem som fel vid åtkomst från företagets mobila program. På samma sätt `301` och `302` anger fel vid åtkomst från den klient som inte stöder omdirigering.

Delvis accepterat innehåll `206` kan tyda på ett fel i en övergripande begäran. Application Insights slutpunkt får exempelvis en batch med telemetri objekt som en enskild begäran. Den returnerar `206` när vissa objekt i batchen inte bearbetades. Öka mängden `206` tyder på problem som behöver undersökas. Liknande logik som gäller för `207` flera Status där framgång kan vara sämsta av separata svarskoder.

Du kan läsa mer på begäran-resultatet kod och statuskod i den [blogginlägget](http://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- [Skriva egen begärandetelemetri](app-insights-api-custom-events-metrics.md#trackrequest)
- Se [datamodellen](application-insights-data-model.md) för Application Insights och modellen.
- Lär dig hur du [Konfigurera ASP.NET Core](app-insights-asp-net.md) program med Application Insights.
- Kolla in [plattformar](app-insights-platforms.md) stöds av Application Insights.
