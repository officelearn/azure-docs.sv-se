---
title: Datamodell för Azure Application Insights telemetri – Beroendetelemetri | Microsoft Docs
description: Application Insights-datamodell för telemetri om beroenden
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 4db0deac72adc5e51294cf7e4c6da334259a5531
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000444"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Beroendetelemetri: Application Insights-datamodell

Beroendetelemetri (i [Application Insights](../../application-insights/app-insights-overview.md)) representerar en interaktion för den övervakade komponenten med en fjärransluten komponent, till exempel SQL eller en HTTP-slutpunkt.

## <a name="name"></a>Namn

Namnet på det kommando som initieras med det här beroendeanropet. Låg kardinalitet värde. Exempel är lagrat procedurnamn och URL: en mallsökvägen.

## <a name="id"></a>ID

Identifierare för en instans för beroende anrop. Används för korrelation med begärandetelemetriobjekt som motsvarar det här beroendeanropet. Mer information finns i [korrelation](../../azure-monitor/app/correlation.md) sidan.

## <a name="data"></a>Data

Kommandot som initieras av det här beroendeanropet. Exempel är SQL-instruktionen och HTTP-URL med alla Frågeparametrar.

## <a name="type"></a>Typ

Namn på beroende. Kardinalitet för lågt värde för logisk gruppering av beroenden och tolkning av andra fält som commandName och Resultatkod. Exempel är SQL Azure-tabell och HTTP.

## <a name="target"></a>Mål

Målplatsen för ett beroende anrop. Exempel är servernamnet, värdadress. Mer information finns i [korrelation](../../azure-monitor/app/correlation.md) sidan.

## <a name="duration"></a>Varaktighet

Varaktighet i format för begäran: `DD.HH:MM:SS.MMMMMM`. Måste vara mindre än `1000` dagar.

## <a name="result-code"></a>Resultatkod

Resultatkod för en beroendeanropet. Exempel är SQL-felkod och HTTP-statuskod.

## <a name="success"></a>Lyckades

Uppgift om lyckade och misslyckade anrop.

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Nästa steg

- Konfigurera beroendespårning för [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Konfigurera beroendespårning för [Java](../../azure-monitor/app/java-agent.md).
- [Skriva anpassade beroendetelemetri](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Se [datamodellen](data-model.md) för Application Insights och modellen.
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) stöds av Application Insights.
