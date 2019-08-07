---
title: Planera din Azure Time Series Insights Preview-miljö | Microsoft Docs
description: Planera Azure Time Series Insights för hands versions miljön.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 72d98e0ca4f75835e7dcc2e7a14b037ccf0d6d7f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840556"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planera Azure Time Series Insights för hands versions miljön

Den här artikeln beskriver metod tips för att planera och komma igång snabbt med hjälp av Azure Time Series Insights för hands version.

> [!NOTE]
> För bästa praxis för att planera en allmän tillgänglighet Time Series Insights instans, se [Planera din Azure Time Series Insights generell tillgänglighets miljö](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Metod tips för planering och förberedelser

För att komma igång med Time Series Insights är det bäst om du förstår:

* Vad du får när du [etablerar en Time Series Insights för hands versions miljö](#the-preview-environment).
* Vad dina [Time Series-ID och tidsstämpel-egenskaper är](#configure-time-series-ids-and-timestamp-properties).
* Vad den nya [tids serie modellen är](#understand-the-time-series-model)och hur du skapar en egen.
* Hur du [skickar händelser effektivt i JSON](#shape-your-events).
* Time Series Insights [affärs haveri återställnings alternativ](#business-disaster-recovery).

Azure Time Series Insights använder en affärs modell enligt principen betala per användning. Mer information om avgifter och kapacitet finns [Time Series Insights prissättning](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>För hands versions miljön

När du etablerar en Time Series Insights för hands versions miljö skapar du två Azure-resurser:

* En Azure Time Series Insights för hands versions miljö
* Ett Azure Storage allmänt v1-konto

För att starta måste du ha tre ytterligare objekt:

* En [tids serie modell](./time-series-insights-update-tsm.md)
* En [händelse källa som är ansluten till Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Händelser som flödar till händelse källan](./time-series-insights-send-events.md) som båda mappas till modellen och är i giltigt JSON-format

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurera Time Series-ID: n och egenskaper för tidsstämpel

Om du vill skapa en ny Time Series Insights miljö väljer du ett Time Series-ID. Detta fungerar som logisk partition för dina data. Se till att dina Time Series ID är klara.

> [!IMPORTANT]
> Time Series-ID : n är oföränderliga och *kan inte ändras senare*. Verifiera var och en innan du väljer den sista och Använd den första.

Du kan välja upp till tre nycklar för att unikt särskilja dina resurser. Mer information finns [i metod tips för att välja ett Time Series-ID](./time-series-insights-update-how-to-id.md) och [lagrings utrymme](./time-series-insights-update-storage-ingress.md).

Egenskapen timestamp är också viktig. Du kan ange den här egenskapen när du lägger till händelse källor. Varje händelse källa har en valfri tidstämpel egenskap som används för att spåra händelse källor över tid. Tidsstämplar-värden är Skift läges känsliga och måste formateras till den enskilda specifikationen för varje händelse källa.

> [!TIP]
> Kontrol lera formaterings-och tolknings kraven för dina händelse källor.

När värdet är tomt används händelsens tidsintervall för händelse källan som händelsens tidsstämpel. Om du skickar historiska data eller batch-händelser är det mer praktiskt att anpassa egenskapen timestamp än standard händelsens kötid. Mer information finns i så här lägger du till [händelse källor i Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Förstå tids serie modellen

Nu kan du konfigurera din Time Series Insights Miljös tids serie modell. Den nya modellen gör det enkelt att hitta och analysera IoT-data. Det möjliggör hantering, underhåll och anrikning av tids serie data och hjälper till att förbereda konsument klara data uppsättningar. Modellen använder Time Series-ID: n, som mappar till en instans som associerar den unika resursen med variabler, som kallas typer och hierarkier. Läs om den nya [tids serie modellen](./time-series-insights-update-tsm.md).

Modellen är dynamisk, så den kan skapas när som helst. Kom igång snabbt genom att skapa och ladda upp den innan du överför data till Time Series Insights. Information om hur du skapar din modell finns i [använda Time Series-modellen](./time-series-insights-update-how-to-tsm.md).

För många kunder mappas Time Series-modellen till en befintlig till gångs modell eller ERP-system redan på plats. Om du inte har en befintlig modell [tillhandahålls](https://github.com/Microsoft/tsiclient) en fördefinierad användar upplevelse för att snabbt komma igång. Om du vill Envision hur en modell kan hjälpa dig kan du Visa [exempel demonstrations miljön](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Forma dina händelser

Du kan kontrol lera hur du skickar händelser till Time Series Insights. Vi rekommenderar att händelserna är denormaliserade och effektivt.

En lämplig tumregel:

* Lagra metadata i din tids serie modell.
* Time Series-läge, instans fält och händelser inkluderar endast nödvändig information, till exempel ett Time Series-ID eller tidsstämpel.

Mer information finns i [form händelser](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nästa steg

- Granska [Azure Advisor](../advisor/advisor-overview.md) för att planera dina konfigurations alternativ för företags återställning.

- Läs mer om [lagring och ingress](./time-series-insights-update-storage-ingress.md) i Time Series Insights för hands versionen.

- Lär dig mer om [data modellering](./time-series-insights-update-tsm.md) i Time Series Insights för hands versionen.