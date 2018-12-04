---
title: Planera din miljö för Azure Time Series Insights (förhandsversion) | Microsoft Docs
description: Planera din miljö för Azure Time Series Insights (förhandsversion)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 438d71997d2c92e377cd068615d274af6b8b5edb
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856950"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planera din miljö för Azure Time Series Insights (förhandsversion)

Den här artikeln beskriver Metodtips för att planera och komma igång snabbt med Azure Time Series Insights (förhandsversion).

## <a name="best-practices-for-planning-and-preparation"></a>Bästa praxis för planering och förberedelser

Det är bäst att ha följande till hands innan du börjar:

* Du har identifierat dina **Time Series-ID: N**
* Du har din **tidsstämpel** egenskap som är redo
* Du har skapat din **Tidsseriemodell**
* Du förstår hur man skickar händelser som är effektivt Avnormaliserade i JSON

Med dessa objekt redo bidrar till att förenkla planering och förberedelser. Dessutom är det klokt att planera i förväg och avgöra din haveriberedskap (BCDR) för företag behöver innan du skapar din instans (och inte efteråt). Då förbereds i förväg hjälper dig för att kontrollera din instans är högst förberedd.

> [!TIP]
> Konfigurera din miljö så att den passar dina behov för BCDR tidigare och inte när du har skapat din instans.

Azure TSI (förhandsversion) använder en användningsbaserad affärsmodell. Mer information om kostnader och kapacitet finns i [Time Series Insights priser](https://azure.microsoft.com/pricing/details/time-series-insights).

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Konfigurera egenskaper för Time Series-ID och tidsstämpel

Om du vill skapa en ny TSI-miljö, Välj en **Time Series-ID**. Detta fungerar så som en logisk partition för dina data. Enligt vad som anges, se till att ha ditt **Time Series-ID: N** redo.

> [!IMPORTANT]
> **Time Series-ID: N** är **oföränderligt** och **kan inte ändras senare**. Kontrollera varje innan val och första användning.

Du kan välja upp till **tre** (3) för att unikt skilja dina resurser. Läs den [bästa praxis för att välja en Time-ID](./time-series-insights-update-how-to-id.md) artikeln för mer information.

Varje händelsekälla har en valfri **tidsstämpel** egenskap som används för att spåra händelsekällor över tid. **Tidsstämpel** värden är skiftlägeskänsliga och måste vara formaterad enskilda varje händelsekälla-specifikationen.

> [!TIP]
> Kontrollera formatering och parsning kraven för din händelsekällor.

När lämnas tomt används det **sätta Händelsetid** källa används av en händelse som händelsen **tidsstämpel**. Om du skickar historiska data eller gruppbaserade händelser du att finna anpassa den **tidsstämpel** egenskap som är bättre än standardvärdet **sätta Händelsetid**. Mer information finns i avsnittet om [hur du lägger till händelsekällor i IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).  

## <a name="understand-the-time-series-model"></a>Förstå tidsserien modell

Du kan nu konfigurera miljön TSI **Tidsseriemodell**. Den nya modellen gör det enkelt att söka efter och analysera IoT-data. Det sker genom att aktivera den hantering och underhåll berikande av time series-data och hjälper dig för att förbereda konsumenter datauppsättningar. Modellen använder **Time Series-ID: N**, som mappar till en instans som kopplar unika resursen till variabler (kallas typer) och hierarkier. Läs mer om den nya [Tidsseriemodell](./time-series-insights-update-tsm.md).

Modellen är dynamiskt så att den kan byggd när som helst. Dock kommer du att kunna komma igång snabbare om den har skapats och laddat upp innan du börjar att skicka data till TSI. Om du vill skapa din modell, granska de [Tidsseriemodell](./time-series-insights-update-tsm.md) artikeln.

För många kunder vi förväntar oss den **Tidsseriemodell** att mappa till en befintlig tillgången modell eller ERP-systemet redan på plats. För de kunder som inte har en befintlig modell, en färdiga användarupplevelsen är [tillhandahålls](https://github.com/Microsoft/tsiclient) att komma igång snabbt.

## <a name="shaping-your-events"></a>Forma dina händelser

Det är viktigt att kontrollera hur du skickar händelser till TSI. Vi rekommenderar avnormaliseras händelser väl och effektivt.

En bra tumregel:

* Metadata ska lagras i din **Tidsseriemodell**
* **Time Series-läget.**  instansfält och händelser bör endast nödvändig information som:
  * **Time Series-ID**
  * **Tidsstämpel**

Granska den [så formhändelser](./time-series-insights-update-how-to-shape-events.md) artikeln för mer detaljer.

## <a name="business-disaster-recovery"></a>Haveriberedskap för företag

TSI är en tjänst med hög tillgänglighet (HA) med hjälp av uppsägningar på nivån Azure-region som en Azure-tjänst. Ingen konfiguration krävs för att använda dessa inbyggda funktioner. Microsoft Azure-plattformen innehåller också funktioner för att hjälpa dig att skapa lösningar med funktioner för katastrofåterställning (DR) eller interregionala tillgänglighet. Om du vill ange globala, hög tillgänglighet över flera regioner för enheter eller användare, dra nytta av dessa Azure-DR-funktioner. Artikeln [Azure Business Continuity teknisk vägledning](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance) beskrivs de inbyggda funktionerna i Azure för affärskontinuitet och Katastrofåterställning. Den [Haveriberedskap](https://docs.microsoft.com/azure/architecture/resiliency/index) återställning och hög tillgänglighet för Azure-program-dokumentet innehåller vägledning för arkitektur om strategier för Azure-program att uppnå hög tillgänglighet och Katastrofåterställning.

> [!NOTE]
> Azure Time Series Insights har inte inbyggda BCDR.
> Som standard har inbyggd återställning av Azure Storage, Azure IoT Hub och Event Hubs.

Mer information:

* Läs mer om [Azure lagringsredundans](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* Läs mer om [IoT-hubbens HA DR](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr).
* Läs mer om [Event Hub principer](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).

Kunder som behöver BCDR kan dock fortfarande implementera en strategi genom att skapa en andra TSI-miljö i en säkerhetskopia av Azure-region. Kunder skicka händelser till den här sekundära miljön från den primära händelsekällan med hjälp av en andra dedikerad konsumentgrupp och den händelsekälla BCDR riktlinjer.

Särskilda åtgärder för att åstadkomma detta är följande:

1. Skapa en miljö i en andra region. Läs mer om [TSI miljöer](./time-series-insights-get-started.md).
1. Skapa en andra dedikerad konsumentgrupp för din händelsekälla och ansluta den händelsekällan till den nya miljön. Glöm inte att ange den andra, dedikerad konsumentgruppen. Läs mer i den [dokumentation för IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) eller [dokumentation för Event Hub](./time-series-insights-data-access.md).
1. Om din primära region som har påverkats under en katastrofåterställning incident, kan du dirigera om åtgärder för att säkerhetskopiera TSI-miljön.

> [!IMPORTANT]
> * Observera att det kan uppstå en fördröjning i händelse av redundans.
> * Redundans kan också göra en mometary topp vid bearbetningen av meddelandet som operations omdirigeras.
> * Mer information finns [hur du minimerar svarstiden i TSD](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>Nästa steg

Läs den [Azure TSI (förhandsversion) lagring och Ingångsanspråk](./time-series-insights-update-storage-ingress.md).

Läs mer om den nya [Tidsseriemodell](./time-series-insights-update-tsm.md).