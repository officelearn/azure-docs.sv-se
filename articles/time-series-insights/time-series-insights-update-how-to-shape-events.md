---
title: Formhändelser – Insikter i Azure Time Series | Microsoft-dokument
description: Lär dig mer om metodtips och hur du utformar händelser för frågor i förhandsversionen av Azure Time Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650931"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Formhändelser med förhandsversionen av Azure Time Series Insights

I den här artikeln beskrivs metodtips för att forma dina JSON-nyttolaster för inmatning i Azure Time Series Insights och för att maximera effektiviteten i dina förhandsfrågor.

## <a name="best-practices"></a>Bästa praxis

Det är bäst att noggrant överväga hur du skickar händelser till din Time Series Insights Preview-miljö. 

Allmänna metodtips är:

* Skicka data över nätverket så effektivt som möjligt.
* Lagra dina data på ett sätt som hjälper dig att aggregera dem mer lämpligt för ditt scenario.

För bästa frågeprestanda följer du följande tumregler:

* Skicka inte onödiga egenskaper. Time Series Insights Förhandsgranska räkningar efter användning. Det är bäst att lagra och bearbeta endast de data som du frågar efter.
* Använd instansfält för statiska data. Den här metoden hjälper till att undvika att skicka statiska data över nätverket. Instansfält, en komponent i tidsseriemodellen, fungerar som referensdata i tjänsten Time Series Insights som är allmänt tillgänglig. Mer information om förekomstfält finns i [Tidsseriemodell](./time-series-insights-update-tsm.md).
* Dela dimensionsegenskaper mellan två eller flera händelser. Den här metoden hjälper dig att skicka data över nätverket mer effektivt.
* Använd inte djup arraykapsling. Time Series Insights Preview stöder upp till två nivåer av kapslade matriser som innehåller objekt. Time Series Insights Preview plattar matriser i meddelanden till flera händelser med egenskapsvärdepar.
* Om det bara finns ett fåtal mått för alla eller de flesta händelser är det bättre att skicka dessa mått som separata egenskaper inom samma objekt. Om du skickar dem separat minskar antalet händelser och kan frågeprestanda förbättras eftersom färre händelser behöver bearbetas.

## <a name="column-flattening"></a>Kolumn förenkling

Under inmatning förenklas nyttolaster som innehåller kapslade objekt så att kolumnnamnet är ett enda värde med en avgränsare.

* Följande kapslade JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Blir: `data_flow` när tillplattad.

> [!IMPORTANT]
> * Förhandsversionen av Azure Time Series`_`Insights använder understreck ( ) för kolumndelineation.
> * Observera skillnaden från allmän tillgänglighet som`.`använder perioder ( ) i stället.

Mer komplexa scenarier illustreras nedan.

#### <a name="example-1"></a>Exempel 1:

Följande scenario har två (eller flera) enheter som skickar mätningarna (signaler): *Flödeshastighet*, *Motoroljetryck*, *Temperatur*och *Luftfuktighet*.

Det finns ett enda Azure IoT Hub-meddelande som skickas där den yttre matrisen innehåller en delad del av gemensamma dimensionsvärden (observera de två enhetsposterna i meddelandet).

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

**Hämtställen:**

* Exemplet JSON har en yttre matris som använder [tidsserieinstansdata](./time-series-insights-update-tsm.md#time-series-model-instances) för att öka meddelandets effektivitet. Även om det inte är troligt att enhetsmetadata ändras i Tidsserieinstanser ger den ofta användbara egenskaper för dataanalys.

* JSON kombinerar två eller flera meddelanden (ett från varje enhet) till en enda nyttolast som sparar bandbredd över tid.

* Enskilda seriedatapunkter för varje enhet kombineras till ett attribut i en **serie** som minskar behovet av att kontinuerligt strömma uppdateringar för varje enhet.

> [!TIP]
> Om du vill minska antalet meddelanden som krävs för att skicka data och göra telemetri mer effektiv bör du överväga att batcha gemensamma dimensionsvärden och metadata för tidsserieinstansen till en enda JSON-nyttolast.

#### <a name="time-series-instance"></a>Tidsserieinstans 

Låt oss ta en närmare titt på hur du använder [Time Series-instans](./time-series-insights-update-tsm.md#time-series-model-instances) för att forma din JSON mer optimalt. 

> [!NOTE]
> [Tidsserie-ID:n](./time-series-insights-update-how-to-id.md) nedan är *deviceIds*.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Förhandsversionen av Time Series Insights sammanfogar en tabell (efter förenkling) under frågetiden. Tabellen innehåller ytterligare kolumner, till exempel **Typ**.

| deviceId  | Typ | L1 (på andra) | L2 (på andra) | timestamp | series_Flow ränta ft3/s | series_Engine oljetryck psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | Simulator | Batterisystem | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | Simulator |   Batterisystem |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA GEMENSAMMA | Simulator |    Batterisystem |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  Tabellen föregående representerar frågevyn i [Utforskaren för förhandsgranskning av tidsserier](./time-series-insights-update-explorer.md).

**Hämtställen:**

* I föregående exempel lagras statiska egenskaper i förhandsversionen av Time Series Insights för att optimera data som skickas över nätverket.
* Förhandsversionsdata för Time Series Insights sammanfogas vid frågetillfället via tidsserie-ID:n som definieras i instansen.
* Två lager häckning används. Det här numret är det mesta som Time Series Insights Preview stöder. Det är viktigt att undvika djupt kapslade matriser.
* Eftersom det finns få mått skickas de som separata egenskaper i samma objekt. I exemplet **är series_Flow Rate psi**, **series_Engine Oil Pressure psi**och **series_Flow Rate ft3/s** unika kolumner.

>[!IMPORTANT]
> Instansfält lagras inte med telemetri. De lagras med metadata i Time Series-modellen.

#### <a name="example-2"></a>Exempel 2:

Tänk på följande JSON:

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```

I exemplet ovan skulle `data["flow"]` den tillplattade egenskapen `data_flow` presentera en namngivningskollision med egenskapen.

I det här fallet skulle det *senaste* egenskapsvärdet skriva över det tidigare värdet. 

> [!TIP]
> Kontakta Time Series Insights-teamet för mer hjälp!

> [!WARNING] 
> * I de fall där dubblettegenskaper finns i samma (singular) händelse nyttolast på grund av förenkling eller annan mekanism, den senaste > egenskapsvärdet lagras, över-skriva eventuella tidigare värden.
> * Serie kombinerade händelser åsidosätter inte varandra.

## <a name="next-steps"></a>Nästa steg

* Om du vill omsätta dessa riktlinjer i praktiken läser du [frågesyntaxen för förhandsfläter för Azure Time Series Insights](./time-series-insights-query-data-csharp.md). Du lär dig mer om frågesyntaxen för tidsseriestatistiken [förhandsgranska REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview) för dataåtkomst.

* Kombinera JSON:s metodtips med [How to Time Series Model](./time-series-insights-update-how-to-tsm.md).
