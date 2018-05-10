---
title: Azure Application Insights telemetri-datamodell - Beroendetelemetri | Microsoft Docs
description: Application Insights datamodellen för beroendetelemetri
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: 019b24839c20e7f8f46eeccf4a7b9622d18b0ad6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Beroendetelemetri: Application Insights-datamodell

Beroendetelemetri (i [Programinsikter](app-insights-overview.md)) representerar en interaktion för den övervakade komponenten med en fjärransluten komponent, till exempel SQL eller en HTTP-slutpunkt.

## <a name="name"></a>Namn

Namnet på det kommando som initieras med det här beroendeanropet. Låg kardinalitet värde. Exempel är namnet på lagrade proceduren och mallen för URL-sökväg.

## <a name="id"></a>ID

Identifierare för ett beroende anropet instans. Används för korrelation med det begärda telemetri elementet som motsvarar det här beroendeanropet. Mer information finns i [korrelation](application-insights-correlation.md) sidan.

## <a name="data"></a>Data

Kommandot som initierats av det här beroendeanropet. Exempel är SQL-instruktionen och HTTP-URL med alla Frågeparametrar.

## <a name="type"></a>Typ

Beroende typnamn. Låg kardinalitet värde för logisk gruppering av beroenden och tolkning av andra fält som commandName och resultCode. Exempel är SQL Azure-tabellen och HTTP.

## <a name="target"></a>Mål

Målplatsen för en beroendeanropet. Exempel är servernamnet, värdadress. Mer information finns i [korrelation](application-insights-correlation.md) sidan.

## <a name="duration"></a>Varaktighet

Begär tid i formatet: `DD.HH:MM:SS.MMMMMM`. Måste vara mindre än `1000` dagar.

## <a name="result-code"></a>Resultatkod

Resultatkod för en beroendeanropet. Exempel är SQL-felkod och HTTP-statuskod.

## <a name="success"></a>Lyckades

Uppgift om lyckade och misslyckade anrop.

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Nästa steg

- Konfigurera spårning för beroende [.NET](app-insights-asp-net-dependencies.md).
- Konfigurera spårning för beroende [Java](app-insights-java-agent.md).
- [Skriva anpassade beroendetelemetri](app-insights-api-custom-events-metrics.md#trackdependency)
- Se [datamodellen](application-insights-data-model.md) för Application Insights typer och modell.
- Checka ut [plattformar](app-insights-platforms.md) stöds av Application Insights.
