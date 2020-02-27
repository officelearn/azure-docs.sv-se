---
title: Form händelser – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om bästa praxis och hur du formar händelser för frågor i Azure Time Insights Preview.
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
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650931"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Formhändelser med förhandsversionen av Azure Time Series Insights

Den här artikeln definierar bästa metoder för att forma dina JSON-nyttolaster för inmatning i Azure Time Series Insights och för att maximera effektiviteten hos dina förhands gransknings frågor.

## <a name="best-practices"></a>Bästa praxis

Det är bäst att noggrant överväga hur du skickar händelser till din Time Series Insights Preview-miljö. 

Allmänna metod tips är:

* Skicka data via nätverket så effektivt som möjligt.
* Store dina data på ett sätt som hjälper dig att samla mer lämpligt för ditt scenario.

För bästa prestanda för frågor följer du följande regler för tummen:

* Skicka inte onödiga egenskaper. Time Series Insights för hands versions fakturor per användning. Det är bäst att lagra och bearbeta data som du kommer att fråga.
* Använd instans fälten för statiska data. Den här metoden hjälper till att undvika att skicka statiska data över nätverket. Instans fält, en komponent i tids serie modellen, fungerar som referens data i Time Series Insights tjänst som är allmänt tillgänglig. Läs [tids serie modell](./time-series-insights-update-tsm.md)för mer information om instans fält.
* Dela dimensionsegenskaper mellan två eller flera händelser. Den här metoden kan du skicka data mer effektivt över nätverket.
* Använd inte djupgående matris kapsling. Time Series Insights för hands versionen stöder upp till två nivåer av kapslade matriser som innehåller objekt. Time Series Insights Preview plattar ut matriser i meddelanden till flera händelser med egenskapsvärdepar.
* Om bara det finns några åtgärder för alla eller de flesta händelser, är det bättre att skicka dessa åtgärder som separata egenskaper inom samma objekt. Att skicka dem separat minskar antalet händelser och kan förbättra frågans prestanda eftersom färre händelser behöver bearbetas.

## <a name="column-flattening"></a>Kolumn förenkling

Under inmatningen kommer nytto laster som innehåller kapslade objekt att förenklas så att kolumn namnet är ett enda värde med en avgränsare.

* Till exempel följande kapslade JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Blir: `data_flow` när de förenklas.

> [!IMPORTANT]
> * Azure Time Series Insights för hands versionen använder under streck (`_`) för kolumn destreckning.
> * Observera skillnaden från allmän tillgänglighet som använder punkter (`.`) i stället.

Mer komplexa scenarier illustreras nedan.

#### <a name="example-1"></a>Exempel 1:

I följande scenario finns två (eller fler) enheter som skickar mätningarna (signaler): *flödes frekvens*, *motor olje tryck*, *temperatur*och *fuktighet*.

Det finns ett enda Azure IoT Hub-meddelande som skickas där den yttre matrisen innehåller ett delat avsnitt av vanliga dimensions värden (Observera de två enhets posterna som finns i meddelandet).

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

**Takeaways:**

* JSON-exemplet har en yttre matris som använder [instans data i Time Series](./time-series-insights-update-tsm.md#time-series-model-instances) för att öka effektiviteten för meddelandet. Även om Time Series-instanser av enhetens metadata troligen inte ändras, ger den ofta användbara egenskaper för data analys.

* JSON kombinerar två eller flera meddelanden (ett från varje enhet) till en enda nytto last som sparar på bandbredd över tid.

* Enskilda serie data punkter för varje enhet kombineras till ett enda **serie** -attribut som minskar behovet av att kontinuerligt strömma uppdateringar för varje enhet.

> [!TIP]
> Om du vill minska antalet meddelanden som krävs för att skicka data och göra telemetri mer effektivt, bör du överväga att gruppera vanliga dimensions värden och Time Series instance metadata i en enda JSON-nyttolast.

#### <a name="time-series-instance"></a>Time Series-instans 

Låt oss ta en närmare titt på hur du använder [Time Series-instansen](./time-series-insights-update-tsm.md#time-series-model-instances) för att forma JSON mer optimalt. 

> [!NOTE]
> [Time Series-ID: na](./time-series-insights-update-how-to-id.md) nedan är *deviceIds*.

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

Förhandsversionen av Time Series Insights ansluter till en tabell (efter att förenkla) under fråga körs. Tabellen innehåller ytterligare kolumner, till exempel **typ**.

| deviceId  | Typ | L1 | L2 | timestamp | series_Flow Rate ft3/s | series_Engine Oil press psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Batteri System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Batteri System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | VANLIGA LINE_DATA | SIMULATOR |    Batteri System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  Föregående tabell representerar frågevyn i [tids serie förhands gransknings Utforskaren](./time-series-insights-update-explorer.md).

**Takeaways:**

* I föregående exempel lagras statiska egenskaper i Time Series Insights för hands versionen för att optimera data som skickas över nätverket.
* Time Series Insights för hands versions data kopplas vid tid med hjälp av Time Series-ID: t som definieras i instansen.
* Två kapslings nivåer används. Det här talet är det mest som Time Series Insights för hands versionen stöder. Det är viktigt att undvika djupt kapslade matriser.
* Eftersom det inte finns några mått, skickas de som separata egenskaper inom samma objekt. I exemplet **Series_Flow Rate PSI**, **series_Engine Oil press PSI**och **series_Flow Rate ft3/s** är unika kolumner.

>[!IMPORTANT]
> Instans fält lagras inte med telemetri. De lagras med metadata i tids serie modellen.

#### <a name="example-2"></a>Exempel 2:

Överväg följande JSON:

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

I exemplet ovan skulle den sammanslagna `data["flow"]`-egenskapen ge en namn konflikt med egenskapen `data_flow`.

I det här fallet skulle det *senaste* egenskap svärdet skriva över det tidigare. 

> [!TIP]
> Kontakta Time Series Insights-teamet om du behöver mer hjälp!

> [!WARNING] 
> * I de fall där duplicerade egenskaper finns i samma (en enda) händelse nytto last på grund av förenkling eller en annan mekanism, lagras det senaste > egenskap svärdet och skriver över alla tidigare värden.
> * Serier med kombinerade händelser åsidosätter inte varandra.

## <a name="next-steps"></a>Nästa steg

* För att kunna använda dessa rikt linjer i övningen läser du [Azure Time Series Insights förhandsgranska frågesyntax](./time-series-insights-query-data-csharp.md). Du lär dig mer om frågesyntaxen för Time Series Insights för [hands versionen REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview) för data åtkomst.

* Kombinera metod tips för JSON med [tids serie modellen](./time-series-insights-update-how-to-tsm.md).
