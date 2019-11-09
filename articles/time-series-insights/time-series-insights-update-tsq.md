---
title: Azure Time Series Insights förhandsgranska data frågor | Microsoft Docs
description: Azure Time Series Insights förhandsgranska data frågor.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: e660db5db3d1afc14a3c895e6786d1b6a8b82c13
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832409"
---
# <a name="data-querying"></a>Fråga data

Azure Time Series Insights för hands version aktiverar data frågor om händelser och metadata som lagras i miljön via API: er på offentlig yta. Dessa API: er används också i [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).

Tre primära API-kategorier är tillgängliga i Time Series Insights:

* **Miljö-API**: er: dessa API: er aktiverar frågor i själva Time Series Insightss miljön. Exempel på frågor är listan över miljöer som anroparen har åtkomst till och miljö-metadata.
* **Tids serie modell – fråga (TSM-Q) API: er**: aktiverar åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) i metadata som lagras i miljö delen av tids serie modellen. Exempel är instanser, typer och hierarkier.
* **TSQ-API: er (Time Series Query)** : aktiverar hämtning av telemetri-eller händelse data när de registreras från käll leverantören eller genom att minska data med hjälp av skalära och mängd funktioner lagrade i variabler. Dessa API: er kan utföra åtgärder för att transformera, kombinera och tillämpa beräkningar på tids serie data.

Time Series Insights använder ett omfattande strängbaserade uttrycks språk, ett [Time Series-uttryck (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)för att uttrycka beräkningar.

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure Time Series Insights för hands version av Core API: er

Följande kärn-API: er stöds.

[Översikt över ![tids serie frågor](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Miljö-API: er

Följande miljö-API: er är tillgängliga:

* [Hämta miljö-API](/rest/api/time-series-insights/management/environments/get): returnerar listan över miljöer som anroparen har behörighet att komma åt.
* [Hämta miljö tillgänglighets-API](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): returnerar fördelningen av antal händelser över händelsens tidsstämpel `$ts`. Det här API: et hjälper till att avgöra om det finns några händelser i tidsstämpeln genom att returnera antalet händelser, om sådana finns.
* [Hämta API för händelse schema](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): Returnerar metadata för händelse schema för ett angivet Sök omfång. Med detta API kan du hämta alla metadata och egenskaper som är tillgängliga i schemat för angivet Sök omfång.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series-modell – fråga (TSM-Q) API: er

Följande tids serie modell – fråge-API: er är tillgängliga. De flesta av dessa API: er har stöd för batch-körning för att aktivera CRUD-åtgärder i flera tids serie modell enheter:

* [API för modell inställningar](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): aktiverar *Get* och *patch* för standard typen och modell namnet för miljön.
* [Typ-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): aktiverar CRUD för tids serie typer och deras associerade variabler.
* [Hierarkier API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): aktiverar CRUD i Time Series-hierarkier och deras associerade fält Sök vägar.
* [Instans-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): aktiverar CRUD på Time Series-instanser och deras associerade instans fält. Instans-API: et stöder dessutom följande åtgärder:
  * [Sök](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): hämtar en partiell lista med träffar vid sökning efter Time Series-instanser baserat på instans-attribut.
  * [Föreslå](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): söker efter och föreslår en partiell lista med träffar vid sökning efter Time Series-instanser baserat på instans-attribut.

## <a name="time-series-query-tsq-apis"></a>API: er för Time Series-frågor (TSQ)

Följande API: er för Time Series-frågor är tillgängliga. Dessa API: er är tillgängliga för alla lagrings lager som stöds i Time Series Insights. Fråge-URL-parametrar används för att ange den [lagrings typ](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) som frågan ska köras på:

* [Hämta händelse-API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): aktiverar frågor och hämtning av Time Series Insights data från händelser när de registreras i Time Series Insights från käll leverantören. Med det här API: et kan du hämta rå händelser för ett angivet Time Series-ID och Sök omfång. Detta API stöder sid brytning för att hämta den fullständiga data uppsättningen för den valda indatan. 

* [Hämta serie-API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): möjliggör frågor och hämtning av Time Series Insights data från insamlade händelser med hjälp av data som spelas in i kabeln. De värden som returneras baseras på variablerna som har definierats i modellen eller angavs infogade. Detta API stöder sid brytning för att hämta den fullständiga data uppsättningen för den valda indatan. Det här API: et hjälper till att definiera beräknade egenskaper eller kolumner.

    >[!NOTE]
    > Agg regerings satsen ignoreras även om den har angetts i en modell eller anges infogad.

  API för Get-serien returnerar ett tids serie värde för varje variabel för varje intervall. Ett Time Series-värde är ett format som Time Series Insights används för att mata ut JSON från en fråga. Värdena som returneras baseras på Time Series-ID: t och uppsättningen variabler som tillhandahölls.

* [API för sammanställd serie](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable): möjliggör frågor och hämtning av Time Series Insights data från insamlade händelser genom att sampla och aggregera inspelade data. Detta API stöder fortsättnings bar-körning med hjälp av [tilläggs-token](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage).

  API: t för sammanställd serie returnerar ett tids serie värde för varje variabel för varje intervall. Värdena baseras på Time Series-ID: t och uppsättningen med variabler som tillhandahölls. API: t för sammanställd serie uppnår en minskning med hjälp av variabler som lagras i tids serie modellen eller som anges i mängd-eller exempel data.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [lagring och ingress](./time-series-insights-update-storage-ingress.md) i Azure Time Series Insights Preview.
- Läs artikeln Time Series Insights för hands versions [data modellering](./time-series-insights-update-tsm.md) .
- Identifiera [metod tips för att välja ett Time Series-ID](./time-series-insights-update-how-to-id.md).
