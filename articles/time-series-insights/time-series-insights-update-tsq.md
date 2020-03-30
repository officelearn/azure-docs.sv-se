---
title: Datafrågor i förhandsversion – Insikter i Azure Time Series | Microsoft-dokument
description: Data fråga begrepp och HTTP REST API översikt i Azure Time Series Insights Preview.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284899"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Datafrågor i förhandsversionen av Azure Time Series Insights

Azure Time Series Insights möjliggör datafrågor på händelser och metadata som lagras i miljön via offentliga api:er för ytan. Dessa API:er används också av [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

Tre primära API-kategorier är tillgängliga i Time Series Insights:

* **Miljö-API:er**: Dessa API:er aktiverar frågor om själva time series insights-miljön. Dessa kan användas för att samla in listan över miljöer som anroparen har åtkomst till och miljömetadata.
* **Api:er för tidsseriemodell-frågefrågor (TSM-Q):** Aktiverar åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på metadata som lagras i miljöns tidsseriemodell. Dessa kan användas för att komma åt och redigera instanser, typer och hierarkier.
* **TSQ-API:er (Time Series Query)**: Aktiverar hämtning av telemetri- eller händelsedata när de registreras från källleverantören och möjliggör utförliga beräkningar och aggregeringar på data med hjälp av avancerade skalär- och aggregationsfunktioner.

Time Series Insights använder ett omfattande strängbaserat uttrycksspråk, [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)för att uttrycka beräkningar.

## <a name="azure-time-series-insights-core-apis"></a>Azure Time Series Insights kärn-API:er

Följande grundläggande API:er stöds.

[![Översikt över fråga i Tidsserie](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Api:er för miljö

* [Hämta miljö-API:](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get)Returnerar listan över miljöer som anroparen har behörighet att komma åt.
* [Api för att få miljötillgänglighet:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)Returnerar fördelningen `$ts`av händelseantalet över händelsetidsstämpeln . Det här API:et hjälper dig att avgöra om det finns några händelser i miljön genom att returnera antalet händelser som är uppdelade i tidsintervall, om det finns några.
* [Hämta API för händelseschema:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)Returnerar metadata för händelseschema för ett visst sökintervall. Det här API:et hjälper dig att hämta alla metadata och egenskaper som är tillgängliga i schemat för det angivna sökintervallet.

## <a name="time-series-model-query-tsm-q-apis"></a>Api:er för tidsseriemodellfråga (TSM-Q)

De flesta av dessa API:er stöder batchkörning för att aktivera batch CRUD-åtgärder på flera Time Series-modellentiteter:

* [Api för modellinställningar:](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api)Aktiverar *GET* och *PATCH* på standardtypen och modellnamnet för miljön.
* [Typer API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api)Aktiverar CRUD på time series-typer och tillhörande variabler.
* [Hierarkier API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api)Aktiverar CRUD på Time Series hierarkier och deras associerade fältsökvägar.
* [Instanser API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api)Aktiverar CRUD på Time Series-instanser och deras associerade instansfält. Dessutom stöder instans-API:et följande åtgärder:
  * [Sök](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Hämtar en ofullständig lista med träffar på sökning efter tidsserieinstanser baserat på instansattribut.
  * [Föreslå](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): Söker efter och föreslår en ofullständig lista med träffar på sökning efter tidsserieinstanser baserat på instansattribut.

## <a name="time-series-query-tsq-apis"></a>Tsq-API:er (Time Series Query)

Dessa API:er är tillgängliga på alla båda butikerna i vår lagringslösning med flera lager i Time Series Insights. Fråge-URL-parametrar används för att ange vilken [arkivtyp](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) frågan ska köras på:

* [Hämta api för händelser:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)Aktiverar fråga och hämtning av råa händelser och tillhörande händelsetidsstämplar när de registreras i Time Series Insights från källleverantören. Med det här API:et kan hämtning av råa händelser för ett visst tidsserie-ID och sökintervall. Det här API:et stöder sidnumrering för att hämta den fullständiga svarsdatauppsättningen för den valda indata. 

* [Hämta serie-API:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)Aktiverar fråga och hämtning av beräknade värden och tillhörande händelsetidsstämplar genom att tillämpa beräkningar som definierats av variabler för råhändelser. Dessa variabler kan definieras antingen i tidsseriemodellen eller anges i frågan. Det här API:et stöder sidnumrering för att hämta den fullständiga svarsdatauppsättningen för den valda indata. 

* [Api för aggregerad serie:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)Aktiverar fråga och hämtning av aggregerade värden och tillhörande intervalltidsstämplar genom att tillämpa beräkningar som definierats av variabler för råhändelser. Dessa variabler kan definieras antingen i tidsseriemodellen eller anges i frågan. Det här API:et stöder sidnumrering för att hämta den fullständiga svarsdatauppsättningen för den valda indata. 
  
  För ett angivet sökintervall och intervall returnerar det här API:et ett aggregerat svar per variabel per intervall för ett tidsserie-ID. Antalet intervall i svarsdatauppsättningen beräknas genom att räkna epokkingstystningar (antalet millisekunder som har förflutit sedan Unix-epok - 1 januari 1970) och dividera fästingarna med intervallets intervallstorlek som anges i frågan.

  De tidsstämplar som returneras i svarsuppsättningen är av vänster intervallgränser, inte av de samplade händelserna från intervallet. 

## <a name="next-steps"></a>Nästa steg

- Läs mer om olika variabler som kan definieras i [tidsseriemodellen](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Läs mer om hur du frågar data från [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
