---
title: Data typer som stöds – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om de data typer som stöds i Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c2e70a4f5cdbbc7a5a408138c3ec832cc831cf33
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046572"
---
# <a name="supported-data-types"></a>Data typer som stöds

I följande tabell visas de data typer som stöds av Azure Time Series Insights Gen2

| Datatyp | Beskrivning | Exempel | Egenskaps kolumn namn i Parquet
|---|---|---|---|
| **boolesk** | En datatyp med ett av två tillstånd: `true` eller `false` . | "isQuestionable": true | isQuestionable_bool
| **datetime** | Representerar en omedelbar tid, vanligt vis uttryckt som datum och tid på dagen. Uttryckt i [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) -format. Datetime-egenskaper lagras alltid i UTC-format. Tids zons förskjutningar, om det är korrekt formaterat, används och sedan värden som lagras i UTC. Se [det här](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) avsnittet för mer information om miljöns tidstämpel egenskap och datetime-förskjutningar | "eventProcessedLocalTime": "2020-03-20T09:03:32.8301668 Z" | eventProcessedLocalTime_datetime 
| **Dubbelklicka** | Ett tal med dubbel precision 64-bit  | "värde": 31,0482941 | value_double
| **long** | Ett signerat 64-bitars heltal  | "värde": 31 | value_long
| **sträng** | Text värden måste bestå av giltig UTF-8. |  "plats": "DIM_MLGGG" | site_string
| **dynamisk** | En komplex (icke-primitiv) typ som består av antingen en matris eller en egenskaps uppsättning (ord lista). För närvarande lagras bara JSON-matriser av primitiver eller matriser med objekt som inte innehåller TS-ID: t eller tidsstämpeln som de ska ha som dynamiska. Läs den här [artikeln](./concepts-json-flattening-escaping-rules.md) för att förstå hur objekt kommer att utjämnas och matriser kan avregistreras. Nytto Last egenskaper som lagras som den här typen är tillgängliga via Azure Time Series Insights Gen2 Explorer och GetEvents-fråge-API. |  "värden": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * Din Azure Time Series Insights Gen2-miljö är starkt skriven. Om enheter eller Taggar skickar både heltals-och data som inte är Integral, lagras enhetens egenskaps värden i två separerade dubbel-och långa kolumner och [sammanslagning ()-funktionen](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) ska användas vid API-anrop och för att definiera tids serie modellens variabel uttryck.

#### <a name="objects-and-arrays"></a>Objekt och matriser

Du kan skicka komplexa typer som objekt och matriser som en del av din händelse nytto Last. Kapslade objekt blir förenklade och matriser kan antingen lagras som `dynamic` eller utplattas för att producera flera händelser beroende på din miljö konfiguration och JSON-form. Läs mer om hur du kan läsa mer om [JSON-förenkling och undantags regler](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Nästa steg

* Läs om hur du kommer att lagra händelser genom att läsa mer om [JSON-förenkling och undantags regler](./concepts-json-flattening-escaping-rules.md) . 

* Förstå din miljös [data flödes begränsningar](./concepts-streaming-ingress-throughput-limits.md)

* Lär dig mer om [händelse källor](concepts-streaming-ingestion-event-sources.md) för att mata in strömmande data.
