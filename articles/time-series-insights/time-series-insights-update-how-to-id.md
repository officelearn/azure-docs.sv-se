---
title: Metodtips för att välja ett Time Series-ID - Azure Time Series Insights | Microsoft-dokument
description: Lär dig mer om metodtips när du väljer ett Time Series-ID i förhandsversionen av Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083525"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Metodtips för att välja ett tidsserie-ID

I den här artikeln sammanfattas vikten av Time Series-ID:t för din förhandsversion av Azure Time Series Insights och metodtips för att välja ett.

## <a name="choose-a-time-series-id"></a>Välj ett Time Series-ID

Det är viktigt att välja ett lämpligt tidsserie-ID. Att välja ett tidsserie-ID är som att välja en partitionsnyckel för en databas. Det krävs när du skapar en time series Insights Preview-miljö. 

> [!IMPORTANT]
> Tidsserie-ID:n är:
> * En *skiftlägeskänslig* egenskap: brev- och teckenhöljen används vid sökningar, jämförelser, uppdateringar och vid partitionering.
> * En *oföränderlig* egenskap: När den väl har skapats kan den inte ändras.

> [!TIP]
> Om händelsekällan är en IoT-hubb kommer tidsserie-ID troligen att vara ***iothub-connection-device-id***.

De viktigaste metodtipsen att följa är:

* Välj en partitionsnyckel med många olika värden (till exempel hundratals eller tusentals). I många fall kan det bero på enhets-ID, sensor-ID eller tagg-ID i din JSON.
* Tidsserie-ID:n ska vara unikt på bladnodnivå i [din tidsseriemodell](./time-series-insights-update-tsm.md).
* Teckengränsen för tidsserie-ID:ns egenskapsnamnsträng är 128. För egenskapsvärdet för Tidsserie-ID är teckengränsen 1 024.
* Om ett unikt egenskapsvärde för tidsserie-ID saknas behandlas det som ett null-värde och följer samma regel för unikhetsbegränsningen.
* Du kan också välja upp till *tre* nyckelegenskaper som tidsserie-ID. Deras kombination blir en sammansatt nyckel som representerar tidsserie-ID.  
  > [!NOTE]
  > Dina tre nyckelegenskaper måste vara strängar.
  > Du måste fråga mot den här sammansatta nyckeln i stället för en egenskap i taget.

## <a name="select-more-than-one-key-property"></a>Markera mer än en nyckelegenskap

I följande scenarier beskrivs valet av mer än en nyckelegenskap som tidsserie-ID.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Exempel 1: Tidsserie-ID med en unik nyckel

* Du har arvflottor av tillgångar. Var och en har en unik nyckel.
* En flotta identifieras unikt av egenskapen **deviceId**. För en annan flotta är den unika egenskapen **objectId**. Ingen av flottorna innehåller den andra flottans unika egendom. I det här exemplet väljer du två nycklar, **deviceId** och **objectId**, som unika nycklar.
* Vi accepterar null-värden och avsaknaden av en egenskaps närvaro i händelsenyttolasten räknas som ett null-värde. Detta är också det lämpliga sättet att hantera skicka data till två händelsekällor där data i varje händelsekälla har ett unikt Tidsserie-ID.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Exempel 2: Tidsserie-ID med en sammansatt nyckel

* Du kräver att flera egenskaper är unika inom samma resurspark. 
* Du är en tillverkare av smarta byggnader och distribuera sensorer i varje rum. I varje rum har du vanligtvis samma värden för **sensorId**. Exempel är **sensor1,** **sensor2**och **sensor3**.
* Din byggnad har överlappande golv- och rumsnummer över platser i fastigheten **flrRm**. Dessa tal har värden som **1a,** **2b**och **3a**.
* Du har en egenskap, **plats**, som innehåller värden som **Redmond,** **Barcelona**och **Tokyo**. Om du vill skapa unikhet anger du följande tre egenskaper som time series-ID-nycklar: **sensorId,** **flrRm**och **plats**.

Exempel på rå händelse:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

I Azure-portalen kan du sedan ange den sammansatta nyckeln på följande sätt: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [datamodellering](./time-series-insights-update-tsm.md).

* Planera [din förhandsversion av Azure Time Series Insights](./time-series-insights-update-plan.md).