---
title: Azure Time Series Insights Preview-data och fråga | Microsoft Docs
description: Azure Time Series Insights Preview data frågor.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 7885473d029556e10663675a9886c7ea3b9c709c
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555429"
---
# <a name="data-querying"></a>Fråga data

Azure Time Series Insights Preview möjliggör data frågekörning på händelser och metadata som lagras i miljön via offentliga surface API: er. Dessa API: er används också i den [förhandsversionen av Time Series Insights explorer](./time-series-insights-update-explorer.md).

Tre primära API-kategorier är tillgängliga i Time Series Insights:

* **Miljö-API: er**: Gör det möjligt för frågor över den Time Series Insights-miljön. Exempel på frågor är listan över miljöer som anroparen har åtkomst till och miljö metadata.

* **Time Series modell-fråga (TSM-Q) API: er**: Gör det möjligt för att skapa, läsa, uppdatera och ta bort åtgärder på metadata som lagras i den miljö som en del av time series-modell. Exempel är instanser, typer och hierarkier.

* **Fråga (TSQ) API: er för Time Series**: Aktiverar hämtning av data för tillståndsändringshändelser enligt från käll-providern. Dessa API: er kan utföra åtgärder för att omvandla, kombinera och utföra beräkningar på time series-data.

Den [Time Series uttryck (TSX) språk](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) är en kraftfull fjärde kategori. Time Series modeller används för att aktivera sammansättning av avancerade beräkning.

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure Time Series Insights Preview core API: er

Följande grundläggande API: er som stöds.

![tsq][1]

### <a name="environment-apis"></a>Miljö-API: er

Följande miljö API: er är tillgängliga:

* [Hämta miljö API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): Returnerar listan över miljöer att anroparen har behörighet att komma åt.
* [Få miljö tillgänglighet API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Returnerar fördelningen av händelseantal över tidsstämpel för händelsen `$ts`. Den här API: et avgöra om det finns några händelser i tidsstämpeln genom att returnera antalet händelser, om sådana finns.
* [Hämta Händelseschema API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Returnerar händelsemetadata för schemat för en viss sökintervallet. Den här API: et hjälper till att hämta alla metadata och egenskaper som är tillgängliga i schemat för den angivna sökintervallet.

### <a name="time-series-model-query-tsm-q-apis"></a>Time Series modell-fråga (TSM-Q) API: er

Följande tid serien modell-Query API: er är tillgängliga:

* [Modellera inställningar API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Kan hämta och korrigera på typ av och modellnamnet på miljön.
* [Skriver API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Aktiverar CRUD på Time Series-typer och deras associerade variabler.
* [Hierarkier API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Aktiverar CRUD på Time Series-hierarkier och deras associerade fältet sökvägar.
* [API-instanser](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Aktiverar CRUD på Time Series-instanser och deras associerade instansfält.

### <a name="time-series-query-tsq-apis"></a>Time Series-fråga (TSQ) API: er

Följande Time Series fråga API: er är tillgängliga:

* [Hämta händelser API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Kan fråga och hämtning av Time Series Insights-data från händelser som de är registrerade i Time Series Insights från käll-providern.

* [Hämta serien API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Aktiverar fråga och hämtning av Time Series Insights-data från avbildade händelserna med hjälp av data som registrerats på kabeln. De värden som returneras är baserade på de variabler som har definierats i modellen eller tillhandahålls infogade.

    >[!NOTE]
    > Instruktionen aggregering ignoreras även om den har angetts i en modell eller tillhandahålls infogade.

  API: et för få serien returnerar ett Time Series-värde för varje variabel för varje intervall. Serien är ett format som Time Series Insights använder för utdata JSON från en fråga. De värden som returneras är baserade på Time Series-ID och en uppsättning variabler som har angetts.

* [Aggregera serien API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): Aktiverar fråga och hämtning av Time Series Insights-data från avbildade händelser efter provtagning och sammanställa registreras data.

  Sammanställd serien API: et returnerar ett Time Series-värde för varje variabel för varje intervall. Värdena är baserade på Time Series-ID och en uppsättning variabler som har angetts. API: et för sammanställd serien uppnår minskning med hjälp av variabler lagras i Time Series-modell eller tillhandahålls infogade aggregate- eller exemplet data.

  Sammanställd typer som stöds: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Nästa steg

Läs om:

- [Lagring och inkommande för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-storage-ingress.md)
- [Datamodellering](./time-series-insights-update-tsm.md)
- [Bästa praxis när du väljer en tid-ID](./time-series-insights-update-how-to-id.md)

<!-- Images -->
[1]: media/v2-update-tsq/tsq.png
