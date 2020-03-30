---
title: Beroendedatamodell för Azure Monitor Application Insights
description: Program insights-datamodell för beroendetelemetri
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: ba0d848904d1ba885dc53e2941953d8dfb4864cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671927"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Beroendetelemetri: Datamodell för Application Insights

Beroendetelemetri (i [Application Insights](../../azure-monitor/app/app-insights-overview.md)) representerar en interaktion mellan den övervakade komponenten med en fjärrkomponent som SQL eller en HTTP-slutpunkt.

## <a name="name"></a>Namn

Namnet på det kommando som initierats med det här beroendeanropet. Lågt kardinalitetsvärde. Exempel är lagrat procedurnamn och URL-sökvägsmall.

## <a name="id"></a>ID

Identifierare för en beroendeanropsinstans. Används för korrelation med telemetriobjektet för begäran som motsvarar det här beroendeanropet. Mer information finns på [korrelationssidan.](../../azure-monitor/app/correlation.md)

## <a name="data"></a>Data

Kommando initierat av det här beroendeanropet. Exempel är SQL-uttryck och HTTP-URL med alla frågeparametrar.

## <a name="type"></a>Typ

Namn på beroendetyp. Lågt kardinalitetsvärde för logisk gruppering av beroenden och tolkning av andra fält som commandName och resultCode. Exempel är SQL, Azure-tabell och HTTP.

## <a name="target"></a>Mål

Målplatsen för ett beroendeanrop. Exempel är servernamn, värdadress. Mer information finns på [korrelationssidan.](../../azure-monitor/app/correlation.md)

## <a name="duration"></a>Varaktighet

Begäran varaktighet i `DD.HH:MM:SS.MMMMMM`format: . Det måste `1000` vara mindre än dagar.

## <a name="result-code"></a>Resultatkod

Resultatkod för ett beroendeanrop. Exempel är SQL-felkod och HTTP-statuskod.

## <a name="success"></a>Lyckades

Uppgift om lyckat eller misslyckat samtal.

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Nästa steg

- Ställ in beroendespårning för [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Ställ in beroendespårning för [Java](../../azure-monitor/app/java-agent.md).
- [Skriv anpassad beroendetelemetri](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Se [datamodell](data-model.md) för programinsiktstyper och datamodell.
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
