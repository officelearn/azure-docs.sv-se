---
title: Azure Time Series Insights (förhandsversion)-data och fråga | Microsoft Docs
description: Azure Time Series Insights (förhandsversion) data frågor
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 00ef6eed23d1645320c28123d6670230cdd725c9
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964925"
---
# <a name="data-querying"></a>Fråga data

Azure Time Series Insights (TSI) möjliggör data frågekörning på händelser och metadata som lagras i miljön via offentliga surface API: er. Dessa API: er används också i den [TSI-Utforskaren](./time-series-insights-update-explorer.md).

Det finns tre primära API-kategorier som är tillgängliga i Azure TSI:

* Miljö API: er kan fråga den TSI-miljön, t.ex. listan över miljöer anroparen har åtkomst till miljön metadata, osv.

* Och serien modell-fråga (TSM-Q) API: er aktivera för att skapa, läsa, uppdatera och ta bort åtgärder på metadata som lagras i den miljö som en del av time series-modell. Instanser, typer, hierarkier, t.ex.

* Time series-fråga (TSQ) API: er att aktivera hämtning av data för tillståndsändringshändelser eftersom den registreras från käll-providern, eller kan utföra åtgärder för att omvandla, kombinera och utföra beräkningar på time series-data.

Den [Time Series Uttrycksspråk](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) (TSX) är en kraftfull fjärde, kategori. Time Series modeller (TSM) används för att aktivera sammansättning av avancerade beräkning.

## <a name="azure-time-series-insights-core-apis"></a>Azure Time Series Insights core API: er

Nedan visas core API: er som vi har stöd för.

![tsq][1]

### <a name="the-environment-apis"></a>Miljö-API: er

Här följer miljö API: erna:

* [Hämta miljö API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): returnerar listan över miljöer att anroparen har behörighet att komma åt.
* [Hämta miljö tillgänglighet API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): returnerar fördelningen av händelseantal över tidsstämpel för händelsen `$ts`. Den här API: et avgöra om det finns några händelser i timestamp genom att returnera antalet händelser om det finns.
* [Hämta händelse schemat API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): returnerar metadata för händelse-schemat för en viss sökintervallet. Den här API: et hjälper till att hämta alla metadata/egenskaper som är tillgängliga i schemat för den angivna sökintervallet.

### <a name="time-series-model-query-tsm-q-apis"></a>Time Series modell-fråga (TSM-Q) API: er

Följande är Time Series modell-Query API: erna:

* [Modellera inställningar API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): kan hämta och korrigera på typ av och modellnamnet på miljön.
* [Skriver API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): aktiverar CRUD på Time Series-typerna och deras associerade variabler.
* [Hierarkier API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): aktiverar CRUD på serien Tidshierarkier och deras associerade fältet sökvägar.
* [API-instanser](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): aktiverar CRUD på Time Series-instanser och deras associerade instansfält.

### <a name="the-time-series-query-tsq-apis"></a>API: er för Time Series fråga (TSQ)

Följande är Time Series fråga API: erna:

* [Hämta händelse-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): hämta händelser API kan fråga och hämtning av TSI data från händelser som de är registrerade i Azure TSI från käll-providern.

* [Hämta serien API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): aktiverar fråga och hämtning av Azure TSI data från avbildade händelserna med hjälp av data som registrerats på kabeln med hjälp av variablerna som definierar i modeller eller angivna infogade.

    >[!NOTE]
    > Aggregering-satsen är ignoreras även om angett i en modell eller tillhandahållits infogade.

  API: T för få serien returnerar TSV (serien tidsvärde formatet TSI använder för JSON-utdata från frågan) för varje variabel för varje intervall, baserat på de angivna **Time Series-ID** och en uppsättning angivna variabler.

* [Aggregera serien API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): aktiverar fråga och hämtning av TSI data från avbildade händelser efter provtagning och sammanställa registreras data.

  Sammanställd serien API: et returnerar en TSV för varje variabel för varje intervall, baserat på de angivna **Time Series-ID** och en uppsättning angivna variabler. API: et för sammanställd serien uppnår minskning genom att använda variabler lagras i TSM eller tillhandahålls infogade aggregate- eller exemplet data.

  Sammanställd typer som stöds: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Nästa steg

Läs den [Azure TSI (förhandsversion) lagring och ingående](./time-series-insights-update-storage-ingress.md).

Läs mer om [datamodellering](./time-series-insights-update-tsm.md).

Läs mer om [bästa praxis när du väljer en tid-ID](./time-series-insights-update-how-to-id.md).

<!-- Images -->
[1]: media/v2-update-tsq/tsq.png
