---
title: Planera förhandsversion av Azure Time Series Insights-miljön | Microsoft Docs
description: Planera din förhandsversion av Azure Time Series Insights-miljö.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: b3fab86b2b2f0ad892e02cd089dbd7c45ce601d6
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205761"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planera förhandsversion av Azure Time Series Insights-miljön

Den här artikeln beskriver Metodtips för att planera och komma igång snabbt med Azure Time Series Insights Preview.

> [!NOTE]
> Se [planera Azure Time Series Insights GA-miljön](time-series-insights-environment-planning.md), för bästa praxis för att planera en allmänt tillgänglig TSI-instans.

## <a name="best-practices-for-planning-and-preparation"></a>Bästa praxis för planering och förberedelser

Det är bäst om du vill komma igång med Time Series Insights, om du känner till:

* Vad du får när du [etablera en förhandsversionen av Time Series Insights-miljö](#the-preview-environment).
* Vad din [Time Series-ID: N och tidsstämpeln är](#configure-time-series-ids-and-timestamp-properties).
* Vilka nya [Tidsseriemodell är](#understand-the-time-series-model), och hur du skapar dina egna.
* Så här [effektivt skicka händelser i JSON](#shape-your-events).
* Time Series Insights [företag möjligheterna för katastrofåterställning](#business-disaster-recovery).

Azure Time Series Insights använder en användningsbaserad affärsmodell. Mer information om kostnader och kapacitet finns i [Time Series Insights priser](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Förhandsversionsmiljön

När du etablerar en förhandsversionen av Time Series Insights-miljö kan skapa du två Azure-resurser:

* En förhandsversion av Azure Time Series Insights-miljö
* Ett Azure Storage-allmänna V1-konto

Om du vill starta, behöver du tre ytterligare objekt:

* En [Time Series-modell](./time-series-insights-update-tsm.md).
* En [händelsekälla ansluten till Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md).
* [Händelser som flödar till händelsekällan](./time-series-insights-send-events.md) som mappas båda till modellen och är i ett giltigt JSON-format.

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurera egenskaper för Time Series-ID och tidsstämpel

Om du vill skapa en ny Time Series Insights-miljö, Välj en **Time Series-ID**. Detta fungerar så som en logisk partition för dina data. Enligt vad som anges, se till att ha Time Series-ID: N redo.

> [!IMPORTANT]
> Time Series-ID: N är *oföränderligt* och *kan inte ändras senare*. Kontrollera vart och ett före sista markering och första användning.

Du kan välja upp till tre (3) för att unikt skilja dina resurser. Mer information finns i [bästa praxis för att välja en Time-ID](./time-series-insights-update-how-to-id.md) och [Storage och ingående](./time-series-insights-update-storage-ingress.md).

Tidsstämpel-egenskapen är också viktigt. Du kan ange den här egenskapen när du lägger till händelsekällor. Varje händelsekälla har en valfri tidsstämpel-egenskap som har använt för att spåra händelsekällor över tid. Tidstämpeln är skiftlägeskänsliga och måste vara formaterad enskilda varje händelsekälla-specifikationen.

> [!TIP]
> Kontrollera formatering och parsning kraven för din händelsekällor.

Om fältet lämnas tomt används händelse sätta tidpunkten för en händelsekälla som händelsen tidsstämpel. Om du skickar historiska data eller gruppbaserade händelser, är anpassa tidsstämpel-egenskapen bättre än standardvärdet sätta tidpunkt för händelsen. Mer information finns i avsnittet om [hur du lägger till händelsekällor i IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Förstå tidsserien modell

Du kan nu konfigurera Tidsseriemodell för din Time Series Insights-miljö. Den nya modellen gör det enkelt att söka efter och analysera IoT-data. Den stöder hantering, underhåll och berikande av time series-data och hjälper dig för att förbereda konsumenter datauppsättningar. Modellen använder **Time Series-ID: N**, som mappar till en instans som associerar unik resurs med variabler, typer och hierarkier. Läs mer om den nya [Tidsseriemodell](./time-series-insights-update-tsm.md).

Modellen är dynamisk, så den kan genereras när som helst. Om du vill komma igång snabbt, skapa och ladda upp den innan du skicka data till Time Series Insights. Om du vill skapa din modell, se [använder den Tidsseriemodell](./time-series-insights-update-how-to-tsm.md).

För många kunder Time Series-modell som mappar till en befintlig tillgången modell eller ERP-systemet redan på plats. Om du inte har en befintlig modell, en fördefinierade användarupplevelsen är [tillhandahålls](https://github.com/Microsoft/tsiclient) att komma igång snabbt. För att tänka på hur en modell kan hjälpa dig att det, visa den [exempel demomiljö](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Forma dina händelser

Du kan kontrollera det sätt som du skickar händelser till Time Series Insights. Vi rekommenderar är dina händelser Avnormaliserade väl och effektivt.

En bra tumregel:

* Store-metadata i din Time Series-modell
* Time Series-läge, instansfält och händelser innehåller endast nödvändig information, till exempel en **Time Series-ID** eller **tidsstämpel**.

Mer information finns i [forma händelser](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nästa steg

- Läs mer om [storage och ingående](./time-series-insights-update-storage-ingress.md) i Time Series Insights Preview.

- Lär dig mer om [datamodellering](./time-series-insights-update-tsm.md) i Time Series Insights Preview.