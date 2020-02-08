---
title: Metod tips för att välja ett Time Series-ID – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om metod tips när du väljer ett Time Series ID i Azure Time Series Insights Preview.
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
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083525"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Metodtips för att välja en Time-ID

Den här artikeln sammanfattar vikten av tids serie-ID: t för din Azure Time Series Insights Preview-miljö och bästa praxis för att välja en.

## <a name="choose-a-time-series-id"></a>Välj ett Time Series-ID

Att välja ett lämpligt tids serie-ID är kritiskt. Välja en Time-ID är som en partitionsnyckel för en databas. Det krävs när du skapar en Time Series Insights för hands versions miljö. 

> [!IMPORTANT]
> Time Series-ID: n är:
> * En *SKIFT* läges känslig egenskap: bokstavs-och tecken Skift läge används vid sökningar, jämförelser, uppdateringar och vid partitionering.
> * En *oföränderlig* egenskap: när den har skapats kan den inte ändras.

> [!TIP]
> Om din händelse källa är en IoT-hubb kommer ditt Time Series-ID sannolikt att ***iothub-Connection-Device-ID***.

Viktiga metod tips för att följa är:

* Välj en partitionsnyckel med många distinkta värden (till exempel hundratals eller tusentals). I många fall kan detta vara enhets-ID, sensor-ID eller tagg-ID i din JSON.
* Time Series-ID: t ska vara unikt på lövnivå för din [tids serie modell](./time-series-insights-update-tsm.md).
* Tecken gränsen för tids serie-ID: t för egenskaps namn strängen är 128. Tecken gränsen är 1 024 för Time Series ID: s egenskaps värde.
* Om ett unikt egenskaps värde för Time Series ID saknas, behandlas det som ett null-värde och följer samma regel för unikhetsvillkor.
* Du kan också välja upp till *tre* nyckel egenskaper som tids serie-ID. Deras kombination är en sammansatt nyckel som representerar Time Series-ID: t.  
  > [!NOTE]
  > De tre nyckel egenskaperna måste vara strängar.
  > Du skulle behöva fråga mot den sammansatta nyckeln i stället för en egenskap i taget.

## <a name="select-more-than-one-key-property"></a>Markera fler än en nyckel egenskap

I följande scenarier beskrivs hur du väljer mer än en nyckel egenskap som ditt Time Series-ID.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Exempel 1: Time Series-ID med en unik nyckel

* Du har äldre flotta av till gångar. Var och en har en unik nyckel.
* En flotta identifieras unikt av egenskap **deviceId**. För en annan flotta är den unika egenskapen **ObjectID**. Varken flottan innehåller den andra flottans unika egendom. I det här exemplet väljer du två nycklar, **deviceId** och **ObjectID**, som unika nycklar.
* Vi accepterar NULL-värden och avsaknad av en egenskaps förekomst i händelse nytto lasten räknas som ett null-värde. Detta är också det bästa sättet att hantera sändning av data till två händelse källor där data i varje händelse källa har ett unikt tids serie-ID.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Exempel 2: Time Series-ID med en sammansatt nyckel

* Du behöver flera egenskaper som ska vara unikt inom samma flottan av tillgångar. 
* Du är tillverkare av smarta byggnader och distribuerar sensorer i varje rum. I varje rum har du normalt samma värden för **sensorId**. Exempel är **sensor1**, **sensor2**och **sensor3**.
* Din byggnad har överlappande golv-och rums nummer mellan platser i egenskapen **flrRm**. De här talen har värden som **1a**, **2b**och **3a**.
* Du har en egenskap, **plats**, som innehåller värden som **Redmond**, **Barcelona**och **Tokyo**. För att skapa unikhet anger du följande tre egenskaper som tids serie-ID-nycklar: **sensorId**, **flrRm**och **location**.

Exempel på rå händelse:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

I Azure Portal kan du ange den sammansatta nyckeln enligt följande: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [data modellering](./time-series-insights-update-tsm.md).

* Planera [Azure Time Series Insights för hands versions miljön](./time-series-insights-update-plan.md).