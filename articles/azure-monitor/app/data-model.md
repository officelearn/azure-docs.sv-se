---
title: Telemetridatamodell för Azure Application Insights | Microsoft-dokument
description: Översikt över datamodell för Application Insights
services: application-insights
documentationcenter: .net
manager: carmonm
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 94013a12e1cf48a8007fce2547c200d82a657b71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671888"
---
# <a name="application-insights-telemetry-data-model"></a>Programinsiktsdatamodell

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) skickar telemetri från ditt webbprogram till Azure-portalen, så att du kan analysera prestanda och användning av ditt program. Telemetrimodellen är standardiserad så att det är möjligt att skapa plattforms- och språkoberoende övervakning. 

Data som samlas in av Application Insights modeller denna typiska programkörning mönster:

![Programinsiktsprogrammodell för programinsikter](./media/data-model/application-insights-data-model.png)

Följande typer av telemetri används för att övervaka körningen av din app. Följande tre typer samlas vanligtvis in automatiskt av SDK för Programinsikter från webbprogramramverket:

* [**Begäran**](data-model-request-telemetry.md) - Genereras för att logga en begäran som tas emot av din app. Programstatistik-webbplatsen SDK genererar till exempel automatiskt ett begärandet telemetriobjekt för varje HTTP-begäran som webbappen tar emot. 

    En **åtgärd** är trådarna för körning som bearbetar en begäran. Du kan också [skriva kod](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) för att övervaka andra typer av åtgärder, till exempel en "väckning" i ett webbjobb eller en funktion som regelbundet bearbetar data.  Varje åtgärd har ett ID. Det här ID:t som kan användas för att [gruppera](../../azure-monitor/app/correlation.md) all telemetri som genereras medan appen bearbetar begäran. Varje åtgärd lyckas eller misslyckas och har en tid.
* [**Undantag**](data-model-exception-telemetry.md) - representerar vanligtvis ett undantag som gör att en åtgärd misslyckas.
* [**Beroende**](data-model-dependency-telemetry.md) - Representerar ett anrop från din app till en extern tjänst eller lagring, till exempel ett REST API eller SQL. I ASP.NET definieras beroendeanrop till SQL `System.Data`av . Anrop till HTTP-slutpunkter `System.Net`definieras av . 

Application Insights innehåller ytterligare tre datatyper för anpassad telemetri:

* [Spårning](data-model-trace-telemetry.md) - används antingen direkt eller via ett kort för att implementera diagnostikloggning med `Log4Net` `System.Diagnostics`hjälp av ett instrumenteringsramverk som är bekant för dig, till exempel eller .
* [Händelse](data-model-event-telemetry.md) - som vanligtvis används för att fånga användarinteraktion med din tjänst, för att analysera användningsmönster.
* [Mått](data-model-metric-telemetry.md) - används för att rapportera periodiska skalärmätningar.

Varje telemetriobjekt kan definiera [kontextinformation](data-model-context.md) som programversion eller användarsessions-ID. Kontext är en uppsättning starkt skrivna fält som avblockerar vissa scenarier. När programversionen är korrekt initierad kan Application Insights identifiera nya mönster i programbeteende som korreleras med omdistribution. Sessions-ID kan användas för att beräkna avbrottet eller en problempåverkan på användarna. Beräkning av distinkt antal sessions-ID-värden för vissa misslyckade beroende, felspårning eller kritiskt undantag ger en god förståelse för en effekt.

Application Insights telemetrimodell definierar ett sätt att [korrelera](../../azure-monitor/app/correlation.md) telemetri till den funktion som den är en del av. En begäran kan till exempel ringa en SQL Database-samtal och information om inspelade diagnostik. Du kan ange korrelationskontexten för de telemetriobjekt som binder tillbaka den till begäran telemetri.

## <a name="schema-improvements"></a>Schemaförbättringar

Application Insights datamodell är ett enkelt och grundläggande men kraftfullt sätt att modellera din programtelemetri. Vi strävar efter att hålla modellen enkel och smal för att stödja viktiga scenarier och gör det möjligt att utöka schemat för avancerad användning.

Om du vill rapportera datamodell- eller schemaproblem och förslag använder du GitHub [ApplicationInsights-Home-databasen.](https://github.com/Microsoft/ApplicationInsights-Home/issues)

## <a name="next-steps"></a>Nästa steg

- [Skriva anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md)
- Lär dig hur du [utökar och filtrerar telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- Använd [sampling](../../azure-monitor/app/sampling.md) för att minimera mängden telemetri baserat på datamodell.
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
