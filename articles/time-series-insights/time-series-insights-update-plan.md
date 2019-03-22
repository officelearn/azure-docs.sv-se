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
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 251e95744f57d9b5e42df9bdc3743f4880ff5381
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077004"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planera förhandsversion av Azure Time Series Insights-miljön

Den här artikeln beskriver Metodtips för att planera och komma igång snabbt med Azure Time Series Insights Preview.

## <a name="best-practices-for-planning-and-preparation"></a>Bästa praxis för planering och förberedelser

Det är bäst om du vill komma igång med Time Series Insights, om du känner till:

* Vad du får när du etablerar en förhandsversionen av Time Series Insights-miljö.
* Vilka dina Time Series-ID och tidsstämpel egenskaper är.
* Vad den nya Tidsseriemodell är, och hur du skapar dina egna.
* Så här skickar händelser effektivt i JSON. 
* Time Series Insights företag möjligheterna för katastrofåterställning.

Time Series Insights använder en användningsbaserad affärsmodell. Mer information om kostnader och kapacitet finns i [Time Series Insights priser](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-time-series-insights-preview-environment"></a>Förhandsversionen av Time Series Insights-miljö

När du etablerar en förhandsversionen av Time Series Insights-miljö kan skapa du två Azure-resurser:

* Time Series Insights Preview-miljö
* Azure Storage allmänna V1-konto

Om du vill starta, behöver du tre ytterligare objekt:
 
- En [Time Series-modell](./time-series-insights-update-tsm.md) 
- En [händelsekälla ansluten till Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- [Händelser som flödar till händelsekällan](./time-series-insights-send-events.md) som mappas båda till modellen och är i ett giltigt JSON-format 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Konfigurera egenskaper för Time Series-ID och tidsstämpel

Om du vill skapa en ny Time Series Insights-miljö, Välj en Time Series-ID. Detta fungerar så som en logisk partition för dina data. Enligt vad som anges, se till att ha Time Series-ID: N redo.

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
* Time Series-läge, instansfält och händelser är bara nödvändig information, till exempel:
  * Tidserie-ID
  * Tidsstämpel

Mer information finns i [forma händelser](./time-series-insights-send-events.md#json).

## <a name="business-disaster-recovery"></a>Haveriberedskap för företag

Time Series Insights är en tjänst för hög tillgänglighet som använder uppsägningar på nivån Azure-region. Konfigurationen är inte nödvändigt att använda dessa inbyggda funktioner. Microsoft Azure-plattformen innehåller också funktioner för att hjälpa dig att skapa lösningar med funktioner för katastrofåterställning eller interregionala tillgänglighet. För att tillhandahålla global interregionala hög tillgänglighet för enheter eller användare, dra nytta av dessa funktioner för Azure katastrofåterställning. 

Information om inbyggda funktioner i Azure för affärskontinuitet och haveriberedskap (BCDR) finns i [teknisk vägledning för Azure business continuity](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance). För arkitekturvägledning om strategier för Azure-program att uppnå hög tillgänglighet och katastrofåterställning finns i dokumentet på [haveriberedskap och hög tillgänglighet för Azure-program](https://docs.microsoft.com/azure/architecture/resiliency/index).

> [!NOTE]
> 
>  Time Series Insights har inte inbyggda BCDR.
> Som standard har inbyggd återställning av Azure Storage, Azure IoT Hub och Azure Event Hubs.

Om du vill veta mer kan du läsa om:

* [Redundans i Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Haveriberedskap för IoT Hub hög tillgänglighet](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [Event Hub-principer](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

Du kan implementera en strategi för även om du behöver BCDR. Skapa en andra Time Series Insights-miljö i en säkerhetskopia av Azure-region. Skicka händelser till den här sekundära miljön från din primära händelsekälla. Använd en andra dedikerad konsumentgrupp och den händelsekälla BCDR riktlinjer.

Följ dessa steg för att skapa och använda en sekundär Time Series Insights-miljö.

1. Skapa en miljö i en andra region. Mer information finns i [Time Series Insights-miljöer](./time-series-insights-get-started.md).
1. Skapa en andra dedikerad konsumentgrupp för din händelsekälla. Anslut den händelsekällan till den nya miljön. Glöm inte att ange andra dedikerad konsumentgrupp. Mer information finns i den [dokumentation för IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) eller [dokumentation för Event Hub](./time-series-insights-data-access.md).
1. Om din primära region påverkas vid en katastrof incident, kan du dirigera om åtgärder för att säkerhetskopiera Time Series Insights-miljön.

> [!IMPORTANT]
> * Observera att du kan uppstått en fördröjning i händelse av redundans.
> * Redundans kan också orsaka en tillfällig topp vid bearbetningen av meddelandet som åtgärder dirigeras.
> * Mer information finns i [Minimera svarstid i Time Series Insights](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer kan du läsa om:

- [Lagring och inkommande för Azure Time Series Insights (förhandsversion)](./time-series-insights-update-storage-ingress.md)
- [Datamodellering](./time-series-insights-update-tsm.md)