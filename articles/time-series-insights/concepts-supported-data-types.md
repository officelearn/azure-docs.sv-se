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
ms.date: 07/23/2020
ms.openlocfilehash: 9d29d83ed92ee0fa2217bb1c27fbf6c2fbb3584c
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170888"
---
# <a name="supported-data-types"></a>Datatyper som stöds

I följande tabell visas de data typer som stöds av Azure Time Series Insights Gen2

| Datatyp | Beskrivning | Exempel | Egenskaps kolumn namn i Parquet
|---|---|---|---|
| **boolesk** | En datatyp med ett av två tillstånd: `true` eller `false` . | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | Representerar en omedelbar tid, vanligt vis uttryckt som datum och tid på dagen. Uttryckt i [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) -format. Datetime-egenskaper lagras alltid i UTC-format. Tids zons förskjutningar, om det är korrekt formaterat, används och sedan värden som lagras i UTC. Se [det här](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) avsnittet för mer information om miljöns tidstämpel egenskap och datetime-förskjutningar | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **double** | Ett tal med dubbel precision 64-bit  | `"value": 31.0482941` | value_double
| **long** | Ett signerat 64-bitars heltal  | `"value" : 31` | value_long
| **nollängd** | Text värden måste bestå av giltig UTF-8. Null-och tomma strängar behandlas likadant. |  `"site": "DIM_MLGGG"` | site_string
| **dynamisk** | En komplex (icke-primitiv) typ som består av antingen en matris eller en egenskaps uppsättning (ord lista). För närvarande lagras bara JSON-matriser med primitiver eller matriser med objekt som inte innehåller något av de TS-ID: n eller Tidsstämpelns egenskaper som är dynamiska. Läs den här [artikeln](./concepts-json-flattening-escaping-rules.md) för att förstå hur objekt kommer att förenklas och matriser kan vara avregistrerade. Nytto Last egenskaper som lagras som den här typen är tillgängliga via Azure Time Series Insights Gen2 Explorer och `GetEvents`   fråge-API. |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>Skicka blandade data typer

Din Azure Time Series Insights Gen2-miljö är starkt skriven. Om enheter eller Taggar skickar data av olika typer för en enhets egenskap lagras värdena i två separata kolumner och [sammanslagningen ()-funktionen](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions) ska användas när du definierar variabel uttryck för tids serie modellen i API-anrop.

I Azure Time Series Insights Explorer kan du automatiskt slå samman separata kolumner i samma enhets egenskap. I exemplet nedan skickar sensorn en `PresentValue` egenskap som kan vara både lång eller dubbel. Om du vill fråga efter alla lagrade värden (oavsett datatyp) för `PresentValue` egenskapen väljer `PresentValue (Double | Long)` du och staplarna kommer att sammanslagna åt dig.

[![Automatisk sammanslagning i Utforskaren](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objekt och matriser

Du kan skicka komplexa typer som objekt och matriser som en del av din händelse nytto Last. Kapslade objekt blir förenklade och matriser kan antingen lagras som `dynamic` eller utplattas för att producera flera händelser beroende på din miljö konfiguration och JSON-form. Läs mer om hur du kan läsa mer om [JSON-förenkling och undantags regler](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Nästa steg

* Läs om hur du kommer att lagra händelser genom att läsa mer om [JSON-förenkling och undantags regler](./concepts-json-flattening-escaping-rules.md) .

* Förstå din miljös [data flödes begränsningar](./concepts-streaming-ingress-throughput-limits.md)

* Lär dig mer om [händelse källor](concepts-streaming-ingestion-event-sources.md) för att mata in strömmande data.
