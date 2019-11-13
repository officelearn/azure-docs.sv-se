---
title: Form händelser – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du formar händelser med Azure Time Series Insights för hands version.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/31/2019
ms.custom: seodec18
ms.openlocfilehash: bd1b59ac2037669be021dfad3bf5032b794bef4a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006278"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Formhändelser med förhandsversionen av Azure Time Series Insights

Den här artikeln hjälper dig att forma din JSON-fil för inmatning och för att maximera effektiviteten hos dina Azure Time Series Insights för hands versioner.

## <a name="best-practices"></a>Bästa praxis

Tänk på hur du skickar händelser till Time Series Insights för hands versionen. Nämligen, bör du alltid:

* Skicka data via nätverket så effektivt som möjligt.
* Store dina data på ett sätt som hjälper dig att samla mer lämpligt för ditt scenario.

För bästa prestanda för frågor gör du följande:

* Skicka inte onödiga egenskaper. Time Series Insights Preview debiteras du på din användning. Det är bäst att lagra och bearbeta data som får du fråga.
* Använd instans fälten för statiska data. Den här metoden kan du undvika att skicka statiska data över nätverket. Instans fält, en komponent i tids serie modellen, fungerar som referens data i Time Series Insights tjänst som är allmänt tillgänglig. Mer information om instans fält finns i [tids serie modell](./time-series-insights-update-tsm.md).
* Dela dimensionsegenskaper mellan två eller flera händelser. Den här metoden kan du skicka data mer effektivt över nätverket.
* Använd inte djupgående matris kapsling. Time Series Insights för hands versionen stöder upp till två nivåer av kapslade matriser som innehåller objekt. Time Series Insights Preview plattar ut matriser i meddelanden till flera händelser med egenskapsvärdepar.
* Om bara det finns några åtgärder för alla eller de flesta händelser, är det bättre att skicka dessa åtgärder som separata egenskaper inom samma objekt. Att skicka dem separat minskar antalet händelser och kan förbättra frågans prestanda eftersom färre händelser behöver bearbetas.

Under inmatningen kommer nytto laster som innehåller kapsling att förenklas, så att kolumn namnet är ett enda värde med en avgränsare. I Time Series Insights för hands versionen används under streck för destrykning. Observera att detta är en förändring från den GA versionen av produkten som använde perioder. Under för hands versionen finns det en genom gång av en genom gång, som illustreras i det andra exemplet nedan.

## <a name="examples"></a>Exempel

I följande exempel baseras på ett scenario där två eller flera enheter skicka mätning av faktisk användning eller signaler. Mätningarna eller signalerna kan vara *flödes takt*, *motor olje tryck*, *temperatur*och *fuktighet*.

I exemplet finns det ett enda Azure IoT Hub-meddelande där den yttre matrisen innehåller ett delat avsnitt av vanliga dimensions värden. Yttre matrisen använder Time Series instansdata för att öka effektiviteten för meddelandet. 

Time Series-instansen innehåller metadata för enheten. Dessa metadata ändras inte med varje händelse, men den innehåller användbara egenskaper för data analys. Om du vill spara på byte som skickats över kabeln och göra meddelandet mer effektivt, kan du överväga att gruppera vanliga dimensions värden och använda Time Series instance metadata.

### <a name="example-1"></a>Exempel 1:

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

### <a name="time-series-instance"></a>Time Series-instans 

> [!NOTE]
> Time Series-ID är *deviceId*.

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

Förhandsversionen av Time Series Insights ansluter till en tabell (efter att förenkla) under fråga körs. Tabellen innehåller fler kolumner som **typ**. Följande exempel visar hur du kan [forma](./time-series-insights-send-events.md#supported-json-shapes) dina telemetridata.

| deviceId  | Typ | L1 | L2 | tidsstämpel | series_Flow Rate ft3/s | series_Engine Oil press psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Batteri System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Batteri System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | VANLIGA LINE_DATA | SIMULATOR |    Batteri System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Observera följande punkter i föregående exempel:

* Statiska egenskaper lagras i Time Series Insights Preview att optimera data som skickas över nätverket.
* Time Series Insights för hands versions data kopplas vid tid med hjälp av Time Series-ID: t som definieras i instansen.
* Två kapslings nivåer används. Det här talet är det mest som Time Series Insights för hands versionen stöder. Det är viktigt att undvika djupt kapslade matriser.
* Eftersom det inte finns några mått, skickas de som separata egenskaper inom samma objekt. I exemplet **Series_Flow Rate PSI**, **series_Engine Oil press PSI**och **series_Flow Rate ft3/s** är unika kolumner.

>[!IMPORTANT]
> Instans fält lagras inte med telemetri. De lagras med metadata i tids serie modellen.
> I föregående tabell representerar frågevyn.

### <a name="example-2"></a>Exempel 2:

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
I exemplet ovan skulle den sammanslagna `data_flow`-egenskapen ge en namn konflikt med egenskapen `data_flow`. I det här fallet skulle det *senaste* egenskap svärdet skriva över det tidigare. Kontakta TSD-teamet om problemet presenteras som en utmaning för dina affärs scenarier.

> [!WARNING] 
> I de fall där dubbla egenskaper finns i samma händelse nytto last på grund av förenkling eller någon annan mekanism, lagras det senaste egenskap svärdet, overwritting alla tidigare värden.


## <a name="next-steps"></a>Nästa steg

- Om du vill placera dessa riktlinjer i praktiken, se [förhandsversion av Azure Time Series Insights-frågesyntax](./time-series-insights-query-data-csharp.md). Du lär dig mer om frågesyntaxen för Time Series Insights för hands versionen REST API för data åtkomst.
- Mer information om JSON-former som stöds, se [stöds JSON-former](./time-series-insights-send-events.md#supported-json-shapes).
