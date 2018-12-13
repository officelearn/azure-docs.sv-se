---
title: Bästa praxis när du väljer en tid-ID i Azure Time Series Insights | Microsoft Docs
description: Förstå metodtips när du väljer en tid-ID i Azure Time Series Insights
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 86a23e96a0f9b92b4b0551e41c44cf3249b0f7e3
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873401"
---
# <a name="best-practices-when-choosing-a-time-series-id"></a>Bästa praxis när du väljer en tid-ID

Det här dokumentet beskriver partitionsnyckel Azure Time Series Insights (förhandsversion) den **Time Series-ID**, och bästa praxis att välja en.

## <a name="choose-a-time-series-id"></a>Välj ett Time Series-ID

Valet av den **Time Series-ID** är som att välja en partitionsnyckel för en databas. Därför är ett viktigt beslut som ska göras vid designtillfället. Du kan inte uppdatera en befintlig miljö TSI (förhandsversion) för att använda en annan **Time Series-ID**. Med andra ord, när en miljö som har skapats med en **Time Series-ID**, principen kan inte ändras eftersom det är en egenskap som inte kan ändras.

> [!IMPORTANT]
> Den **Time Series-ID** är skiftlägeskänsliga och kan ändras (kan inte ändras när).

Att välja rätt med det i åtanke **Time Series-ID** är viktigt. Överväg följande egenskaper när du väljer en **Time Series-ID**:

> [!div class="checklist"]
> * Välj ett egenskapsnamn som har ett stort antal värden och har även mönster i databasåtkomst. Det är en bra idé att ha en partitionsnyckel med många distinkta värden (t.ex. hundratals eller tusentals). För många kunder, det här är något som liknar **DeviceID**, **SensorID**, etc. i din JSON.
> * Det bör vara unikt för den lägsta nivån för din [Tidsseriemodell](./time-series-insights-update-tsm.md).
> * En **Time Series-ID** teckensträng för egenskapen namn kan vara upp till 128 tecken och **Time Series-ID** egenskapsvärden kan vara upp till 1 024 tecken.
> * Vissa unika **Time Series-ID** egenskapsvärden saknas, hanteras de som null-värden som ingå i unikhetsbegränsningen.

Du kan också välja upp till **tre** (3) nyckelegenskaper som din **Time Series-ID**.

  > [!NOTE]
  > Din **tre** (3) nyckelegenskaperna måste vara strängar.

Följande scenarier beskriver att välja fler än en nyckelegenskap som din **Time Series-ID**:  

* Scenario 1:

  * Du har äldre fjärranläggning tillgångar som samtliga har unika nycklar. 
  * Till exempel en flotta identifieras unikt med egenskapen **deviceId** och en annan där egenskapen unika är **objectId**.  I båda fjärranläggning finns inte i andra beståndet unik egenskap. I det här exemplet väljer du två nycklar **deviceId**, och **objectId** som unika nycklar. 
  * Vi accepterar null-värden och bristen på en egenskap närvaro i händelsenyttolasten som räknas som en `null` värde.  Detta är också det korrekta sättet att hantera Skicka data till två olika händelsekällor där data i varje händelsekälla hade ett annat unikt **Time Series-ID**.

* Scenario 2:

  * Du behöver flera egenskaper för att visa unikhet i samma flottan av tillgångar. Exempel: Anta att du är en smart byggnad tillverkare och distribuera sensorer i varje rum. I varje rum du vanligtvis har samma värden för **sensorId**, inklusive **sensor1**, **sensor2**, **sensor3**och så vidare.
  * Dessutom kan du har överlappande våning och utrymme mellan platser i egenskapen **flrRm** som innehåller värden som `1a`, `2b`, `3a`och så vidare.
  * Slutligen kan du ha en egenskap **plats** som innehåller värden som `Redmond`, `Barcelona`, `Tokyo`och så vidare. För att skapa unika, skulle du har angett alla tre av de här egenskaperna som din **Time Series-ID** nycklar – **sensorId**, **flrRm**, och **plats**.

## <a name="next-steps"></a>Nästa steg

Läs mer om [datamodellering](./time-series-insights-update-tsm.md).

Planera din [Azure Time Series Insights (förhandsversion) miljö](./time-series-insights-update-plan.md).