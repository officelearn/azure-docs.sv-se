---
title: Bästa praxis för att välja en Time-ID i förhandsversionen av Azure Time Series Insights | Microsoft Docs
description: Förstå metodtips när du väljer en tid-ID i förhandsversionen av Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 44c6c8a55bbcbf76cf48fd17a6e52d188a5e99fc
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556619"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Metodtips för att välja en Time-ID

Den här artikeln beskriver Azure Time Series Insights Preview partitionsnyckel, Time Series-ID och bästa praxis för att välja en.

## <a name="choose-a-time-series-id"></a>Välj ett Time Series-ID

Välja en Time-ID är som en partitionsnyckel för en databas. Det är ett viktigt beslut som ska göras vid designtillfället. Du kan inte uppdatera en befintlig förhandsversionen av Time Series Insights-miljö för att använda en annan Time Series-ID. När en miljö skapas med en Time-ID, är med andra ord en oåterkallelig egenskap som inte kan ändras i principen.

> [!IMPORTANT]
> Time Series-ID är skiftlägeskänsliga och kan ändras (det kan inte ändras efter att det angetts).

Med det i åtanke är det viktigt att välja tillämpligt Time Series-ID. Tänk efter dessa bästa metoder när du väljer en tid-ID:
* Välj ett egenskapsnamn som har ett stort antal värden och har även mönster i databasåtkomst. Det är en bra idé att ha en partitionsnyckel med många distinkta värden (till exempel hundratals eller tusentals). För många kunder, det här är något som DeviceID eller SensorID i din JSON.
* Time Series-ID: T måste vara unika den lägsta nivån för din [Tidsseriemodell](./time-series-insights-update-tsm.md).
* En teckensträng för Time Series-ID-egenskapen namn kan ha upp till 128 tecken och egenskapsvärden för Time Series-ID kan ha högst 1024 tecken.
* Om vissa unika värden för Time Series-ID-egenskapen saknas hanteras de som null-värden som ingå i unikhetsbegränsningen.

Du kan också välja upp till *tre* (3) nyckelegenskaper som din Time Series-identifieraren.

  > [!NOTE]
  > Din *tre* (3) nyckelegenskaperna måste vara strängar.

Följande scenarier beskriver välja fler än en nyckelegenskap som din Time Series-ID:  

### <a name="scenario-1"></a>Scenario 1

* Du har äldre fjärranläggning tillgångar, var och en med en unik nyckel. 
* Till exempel en flotta identifieras unikt med egenskapen *deviceId* och en annan där egenskapen unika är *objectId*. Varken flotta innehåller andra vagnparkens unik egenskap. I det här exemplet markerar du två nycklar, deviceId och objekt-ID som unika nycklar. 
* Vi accepterar null-värden och bristen på en egenskap närvaro i händelsenyttolasten som räknas som en `null` värde. Det är också det korrekta sättet att hantera Skicka data till två olika händelsekällor där data i varje händelsekälla har ett unikt Time Series-ID.

### <a name="scenario-2"></a>Scenario 2

* Du behöver flera egenskaper som ska vara unikt inom samma flottan av tillgångar. 
* Exempel: Anta att du är en smart byggnad tillverkare och distribuera sensorer i varje rum. I varje rum du vanligtvis har samma värden för *sensorId*, till exempel *sensor1*, *sensor2*, och *sensor3*.
* Dessutom byggnaden har överlappande våning och utrymme mellan platser i egenskapen *flrRm*, som har värden som *1a*, *2b*, *3a* och så vidare.
* Slutligen kan du ha en egenskap *plats*, som innehåller värden som *Redmond*, *Barcelona*, och *Tokyo*. För att skapa unika, skulle du ange följande egenskaper som dina Time Series-ID-nycklar: *sensorId*, *flrRm*, och *plats*.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [datamodellering](./time-series-insights-update-tsm.md).
* Planera din [Azure Time Series Insights (förhandsversion) miljö](./time-series-insights-update-plan.md).
