---
title: Planera din förhandsgranskningsmiljö – Insikter i Azure Time Series | Microsoft-dokument
description: Metodtips för att konfigurera, hantera, planera och distribuera din förhandsversion av Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b16d78c9670d05fcec8126c5544d1dd97f6a03bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045717"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planera förhandsgranskningsmiljön för Azure Time Series Insights

I den här artikeln beskrivs metodtips för att planera och komma igång snabbt med hjälp av förhandsversionen av Azure Time Series Insights.

> [!NOTE]
> Om du vill ha metodtips för att planera en instans av tidsserier i allmän tillgänglighet läser du [Planera din allmänna tillgänglighetsmiljö för Azure Time Series Insights](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Bästa praxis för planering och förberedelse

Metodtips kring planering och förberedelse av din miljö beskrivs närmare i följande artiklar:

* Vad du får när du [etablerar en förhandsgranskningsmiljö för timeseriestatistik](#the-preview-environment).
* Vad egenskaperna [för tidsserie-ID:n och tidsstämpeln är](#configure-time-series-ids-and-timestamp-properties).
* Vad den nya [Time Series-modellen är](#understand-the-time-series-model)och hur du bygger din egen.
* Hur man [skickar händelser effektivt i JSON](#shape-your-events).
* Time Series Insights [business disaster recovery options](#business-disaster-recovery).

Azure Time Series Insights använder en affärsmodell för betalning per betalning. Mer information om avgifter och kapacitet finns i [Time Series Insights prissättning](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Förhandsgranskningsmiljön

När du etablerar en förhandsversion av Time Series Insights skapar du två Azure-resurser:

* Förhandsversionsmiljö för Azure Time Series Insights
* Ett Microsoft Azure Storage-V1-konto

Som en del av etableringsprocessen anger du om du vill aktivera ett varmt lager. Warm Store ger dig en nivåindelad frågeupplevelse. När det är aktiverat måste du ange en kvarhållningsperiod mellan 7 och 30 dagar. Frågor som utförs inom den varma lagringsperioden ger i allmänhet snabbare svarstider. När en fråga sträcker sig över den varma lagringsperioden serveras den från fryshuset.

Frågor om varm butik är gratis, medan frågor om kyl- och fryshus medför kostnader. Det är viktigt att förstå frågemönstren och planera konfigurationen av din varma butik därefter. Vi rekommenderar att interaktiva analyser av de senaste uppgifterna finns i din varma butik och mönsteranalys och långsiktiga trender finns i kyla.

> [!NOTE]
> Om du vill läsa mer om hur du frågar dina varma data läser du [API-referensen](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters).

Till att börja med behöver du ytterligare tre objekt:

* En [tidsseriemodell](./time-series-insights-update-tsm.md)
* En [händelsekälla som är ansluten till Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Händelser som flödar in i händelsekällan](./time-series-insights-send-events.md) som både är mappade till modellen och är i giltigt JSON-format

## <a name="review-preview-limits"></a>Granska begränsningar för förhandsgranskning

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurera egenskaper för tidsserie-ID:n och tidsstämpel

Om du vill skapa en ny time series insights-miljö väljer du ett tidsserie-ID. Detta fungerar som en logisk partition för dina data. Som nämnts, se till att ha din Time Series ID redo.

> [!IMPORTANT]
> Tidsserie-ID:n *kan inte ändras senare*. Verifiera var och en före det slutliga valet och första användningen.

Du kan välja upp till tre nycklar för att unikt skilja dina resurser. Mer information finns i [Metodtips för att välja ett tidsserie-ID](./time-series-insights-update-how-to-id.md) och [lagring och inträngning](./time-series-insights-update-storage-ingress.md).

**Egenskapen Timestamp** är också viktig. Du kan ange den här egenskapen när du lägger till händelsekällor. Varje händelsekälla har en valfri tidsstämpelegenskap som används för att spåra händelsekällor över tid. Tidsstämpelvärden är skiftlägeskänsliga och måste formateras enligt den enskilda specifikationen för varje händelsekälla.

> [!TIP]
> Kontrollera kraven för formatering och tolkning för dina händelsekällor.

När händelseen är tom används händelsehändelsetiden för en händelsekälla som händelsetidsstämpel. Om du skickar historiska data eller batchade händelser är det mer användbart att anpassa egenskapen Tidsstämpel än standardhändelsesöktiden. Mer information finns i hur du [lägger till händelsekällor i Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Förstå Time Series-modellen

Nu kan du konfigurera time series insights-miljöns tidsseriemodell. Den nya modellen gör det enkelt att hitta och analysera IoT-data. Det möjliggör kurering, underhåll och anrikning av tidsseriedata och hjälper till att förbereda konsumentklara datauppsättningar. Modellen använder tidsserie-ID:n, som mappas till en instans som associerar den unika resursen med variabler, så kallade typer och hierarkier. Läs om den nya [Time Series-modellen](./time-series-insights-update-tsm.md).

Modellen är dynamisk, så den kan byggas när som helst. För att komma igång snabbt kan du skapa och ladda upp den innan du trycker data till Time Series Insights. Om du vill skapa din modell läser [du Använd tidsseriemodellen](./time-series-insights-update-how-to-tsm.md).

För många kunder mappar Time Series-modellen till en befintlig tillgångsmodell eller affärssystem som redan finns. Om du inte har en befintlig modell [ges](https://github.com/Microsoft/tsiclient) en fördefinierad användarupplevelse för att komma igång snabbt. Om du vill föreställa dig hur en modell kan hjälpa dig kan du visa [exempeldemomiljön](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Forma dina händelser

Du kan verifiera hur du skickar händelser till Time Series Insights. Helst är dina händelser denormaliserade väl och effektivt.

En bra tumregel:

* Lagra metadata i din Tidsseriemodell.
* Kontrollera att tidsserieläge, instansfält och händelser endast innehåller nödvändig information, till exempel en tidsserie-ID eller tidsstämpelegenskap.

Mer information finns i [Formhändelser](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nästa steg

- Granska [Azure Advisor](../advisor/advisor-overview.md) för att planera konfigurationsalternativen för företagsåterställning.
- Läs mer om [lagring och inträngning i](./time-series-insights-update-storage-ingress.md) förhandsversionen av Time Series Insights.
- Läs mer om [datamodellering](./time-series-insights-update-tsm.md) i förhandsversionen av Time Series Insights.
