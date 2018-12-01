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
ms.openlocfilehash: 0f09561c362494d1e41edf29b85dee77dbbc3678
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721603"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Beroendetelemetri: Application Insights-datamodell

Beroendetelemetri (i [Application Insights](app-insights-overview.md)) representerar en interaktion för den övervakade komponenten med en fjärransluten komponent, till exempel SQL eller en HTTP-slutpunkt.

## <a name="name"></a>Namn

Namnet på det kommando som initieras med det här beroendeanropet. Låg kardinalitet värde. Exempel är lagrat procedurnamn och URL: en mallsökvägen.

## <a name="id"></a>ID

Identifierare för en instans för beroende anrop. Används för korrelation med begärandetelemetriobjekt som motsvarar det här beroendeanropet. Mer information finns i [korrelation](application-insights-correlation.md) sidan.

## <a name="data"></a>Data

Kommandot som initieras av det här beroendeanropet. Exempel är SQL-instruktionen och HTTP-URL med alla Frågeparametrar.

## <a name="type"></a>Typ

Namn på beroende. Kardinalitet för lågt värde för logisk gruppering av beroenden och tolkning av andra fält som commandName och Resultatkod. Exempel är SQL Azure-tabell och HTTP.

## <a name="target"></a>Mål

Målplatsen för ett beroende anrop. Exempel är servernamnet, värdadress. Mer information finns i [korrelation](application-insights-correlation.md) sidan.

## <a name="duration"></a>Varaktighet

Varaktighet i format för begäran: `DD.HH:MM:SS.MMMMMM`. Måste vara mindre än `1000` dagar.

## <a name="result-code"></a>Resultatkod

Resultatkod för en beroendeanropet. Exempel är SQL-felkod och HTTP-statuskod.

## <a name="success"></a>Lyckades

Uppgift om lyckade och misslyckade anrop.

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Nästa steg

- Konfigurera beroendespårning för [.NET](app-insights-asp-net-dependencies.md).
- Konfigurera beroendespårning för [Java](app-insights-java-agent.md).
- [Skriva anpassade beroendetelemetri](app-insights-api-custom-events-metrics.md#trackdependency)
- Se [datamodellen](application-insights-data-model.md) för Application Insights och modellen.
- Kolla in [plattformar](app-insights-platforms.md) stöds av Application Insights.
