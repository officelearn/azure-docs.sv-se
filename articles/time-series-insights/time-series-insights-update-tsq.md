---
title: Azure Time Series Insights förhandsgranska data frågor | Microsoft Docs
description: Azure Time Series Insights förhandsgranska data frågor.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 7b7d041b678ccf2a476afc6d0744f6971349677e
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034875"
---
# <a name="data-querying"></a>Fråga data

Azure Time Series Insights för hands version aktiverar data frågor om händelser och metadata som lagras i miljön via API: er på offentlig yta. Dessa API: er används också i [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).

Tre primära API-kategorier är tillgängliga i Time Series Insights:

* **Miljö-API: er**: Aktiverar frågor för själva Time Series Insightss miljön. Exempel på frågor är listan över miljöer som anroparen har åtkomst till och miljö-metadata.

* **Tids serie modell-fråga (TSM-Q) API: er**: Gör att du kan skapa, läsa, uppdatera och ta bort åtgärder på metadata som lagras i miljö delen av tids serie modellen. Exempel är instanser, typer och hierarkier.

* **API: er för Time Series-fråga (TSQ)** : Aktiverar hämtning av händelse data när de registreras från käll leverantören. Dessa API: er kan utföra åtgärder för att transformera, kombinera och utföra beräkningar av tids serie data.

[Time Series Expression-språket (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) är en kraftfull fjärde kategori. Den använder Time Series-modeller för att möjliggöra sammansättning av avancerad beräkning.

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure Time Series Insights för hands version av Core API: er

Följande kärn-API: er stöds.

[Översikt över 1Time Series-frågor @no__t](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Miljö-API: er

Följande miljö-API: er är tillgängliga:

* [Hämta miljö-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): Returnerar listan över miljöer som anroparen har behörighet att komma åt.
* [Hämta miljö tillgänglighets-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Returnerar fördelningen av antal händelser över händelsens tidsstämpel `$ts`. Det här API: et hjälper till att avgöra om det finns några händelser i tidsstämpeln genom att returnera antalet händelser, om sådana finns.
* [Hämta API för händelse schema](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Returnerar metadata för händelse schema för ett angivet Sök omfång. Med detta API kan du hämta alla metadata och egenskaper som är tillgängliga i schemat för angivet Sök omfång.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series-modell – fråga (TSM-Q) API: er

Följande tids serie modell – fråge-API: er är tillgängliga:

* [API för modell inställningar](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Aktiverar get och patch för standard typen och modell namnet för miljön.
* [Typ-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Aktiverar CRUD för tids serie typer och deras associerade variabler.
* [API: er för hierarkier](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Aktiverar CRUD i Time Series-hierarkier och deras associerade fält Sök vägar.
* [Instans-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Aktiverar CRUD på Time Series-instanser och deras associerade instans fält.

## <a name="time-series-query-tsq-apis"></a>API: er för Time Series-frågor (TSQ)

Följande API: er för Time Series-frågor är tillgängliga:

* [Hämta händelse-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Aktiverar frågor och hämtning av Time Series Insights data från händelser när de registreras i Time Series Insights från käll leverantören.

* [Hämta serie-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Aktiverar frågor och hämtning av Time Series Insights data från insamlade händelser med hjälp av data som spelas in i kabeln. De värden som returneras baseras på variablerna som har definierats i modellen eller angavs infogade.

    >[!NOTE]
    > Agg regerings satsen ignoreras även om den har angetts i en modell eller anges infogad.

  API för Get-serien returnerar ett tids serie värde för varje variabel för varje intervall. Ett Time Series-värde är ett format som Time Series Insights används för att mata ut JSON från en fråga. Värdena som returneras baseras på Time Series-ID: t och uppsättningen variabler som tillhandahölls.

* [API för sammanställd serie](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): Gör det möjligt att fråga och hämta Time Series Insights data från insamlade händelser genom att sampla och aggregera inspelade data.

  API: t för sammanställd serie returnerar ett tids serie värde för varje variabel för varje intervall. Värdena baseras på Time Series-ID: t och uppsättningen med variabler som tillhandahölls. API: t för sammanställd serie uppnår en minskning med hjälp av variabler som lagras i tids serie modellen eller som anges i mängd-eller exempel data.

  Samlings typer som stöds: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Nästa steg

- Läs mer om [lagring och ingress](./time-series-insights-update-storage-ingress.md) i Azure Time Series Insights för hands versionen.

- Läs artikeln Time Series Insights för hands versions [data modellering](./time-series-insights-update-tsm.md) .

- Identifiera [bästa praxis när du väljer ett Time Series-ID](./time-series-insights-update-how-to-id.md).
