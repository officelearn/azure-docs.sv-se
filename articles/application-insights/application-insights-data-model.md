---
title: Azure Application Insights telemetri datamodellen | Microsoft Docs
description: Insikter modellen programdata
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: b14eea46e773a4b92ba20cd3121cd258f86307c9
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-telemetry-data-model"></a>Application Insights telemetri datamodellen

[Azure Application Insights](app-insights-overview.md) skickar telemetri från ditt webbprogram till Azure-portalen så att du kan analysera prestanda och användning av programmet. Telemetri modellen är standardiserade så att det är möjligt att skapa språkoberoende övervakning och plattform. 

Data som samlas in av Application Insights modeller detta mönster för körning av vanliga program:

![Application Insights programmodell](./media/application-insights-data-model/application-insights-data-model.png)

Följande typer av telemetri för att övervaka körning av din app. Följande tre typer vanligtvis automatiskt samlas in av Application Insights SDK från web application framework:

* [**Begäran** ](application-insights-data-model-request-telemetry.md) - genererade att logga en begäran tas emot av din app. Till exempel Application Insights web SDK genererar automatiskt en begärandeobjekt telemetri för varje HTTP-begäran som tar emot ditt webbprogram. 

    En **åtgärden** är trådarna i körningen som bearbetar en begäran. Du kan också [skriva kod](app-insights-api-custom-events-metrics.md#trackrequest) bearbetar data att övervaka andra typer av åtgärden, som en ”väcka” på en webbplats jobbet eller fungera som regelbundet.  Varje åtgärd har ett ID. Detta ID kan användas för att [grupp](application-insights-correlation.md) all telemetri som uppstår när din app bearbetar begäran. Varje åtgärd antingen lyckas eller misslyckas och har en tidsperiod.
* [**Undantag** ](application-insights-data-model-exception-telemetry.md) -representerar vanligen ett undantag som gör att åtgärden misslyckades.
* [**Beroende** ](application-insights-data-model-dependency-telemetry.md) -representerar ett anrop från appen till en extern tjänst eller lagring som en REST API eller SQL. I ASP.NET, beroendeanrop till SQL definieras av `System.Data`. Anrop till HTTP-slutpunkter definieras av `System.Net`. 

Application Insights innehåller tre ytterligare datatyper för anpassad telemetri:

* [Spåra](application-insights-data-model-trace-telemetry.md) – användas antingen direkt eller via en adapter för att implementera diagnostikloggning med hjälp av ett ramverk för instrumentation som är bekant, som `Log4Net` eller `System.Diagnostics`.
* [Händelsen](application-insights-data-model-event-telemetry.md) - brukar användas för att avbilda användarinteraktion med din tjänst för att analysera användningsmönster.
* [Måttet](application-insights-data-model-metric-telemetry.md) – används för att rapporten periodiska skalära mätningar.

Varje telemetri-objekt kan definiera den [omständighetsinformation](application-insights-data-model-context.md) som programmets version eller användaren sessions-id. Kontexten är en uppsättning strikt typkontroll fält som avblockeras vissa scenarier. När programversion har initierats korrekt kan Application Insights identifiera nya mönster i programmets beteende som samverkar med omdistributionen. Sessions-id kan användas för att beräkna avbrottet eller ett problem inverkan på användarna. Räknar antalet distinkta av session-ID-värden för vissa misslyckades beroende, fel spårningen eller kritiskt undantag ger en god förståelse av konsekvenser.

Programmodell insikter telemetri definierar ett sätt att [korrelera](application-insights-correlation.md) telemetri till åtgärden som den är en del. En begäran kan ringa en SQL-databas och registreras diagnostik information. Du kan ange telemetri objekt som binder till begärandetelemetri korrelation kontext.

## <a name="schema-improvements"></a>Förbättringar av schemat

Application Insights-datamodell är ett enkelt och grundläggande men kraftfullt sätt att modellera programmets telemetri. Vi strävar efter att hålla modellen enkel och smidig som stöder viktiga scenarier och tillåta för att utöka schemat för avancerad användning.

Rapportera problem med data modell eller schemat och förslag använder GitHub [ApplicationInsights hem](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) databasen.

## <a name="next-steps"></a>Nästa steg

- [Skriv anpassad telemetri](app-insights-api-custom-events-metrics.md)
- Lär dig hur du [utöka och filtrera telemetri](app-insights-api-filtering-sampling.md).
- Använd [provtagning](app-insights-sampling.md) att minimera telemetri som baseras på datamodellen.
- Checka ut [plattformar](app-insights-platforms.md) stöds av Application Insights.
