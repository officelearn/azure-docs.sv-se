---
title: Metodtips för shaping JSON i Azure tid serien Insights frågor.
description: Lär dig att förbättra effektiviteten tid serien insikter frågan.
services: time-series-insights
author: ashannon7
manager: timlt
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: bryanla
ms.openlocfilehash: 29919a3ce8c18982c88f7f0e6bbd774c612e9c44
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661091"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Så här formen JSON att maximera prestanda för frågor 

Den här artikeln innehåller anvisningar för shaping JSON att maximera effektiviteten hos dina Azure tid serien insikter (TSD)-frågor.

## <a name="best-practices"></a>Bästa praxis

Det är viktigt att tänka på hur du skickar händelser till tid serien insikter. Nämligen bör du alltid:

1. Skicka data via nätverket så effektivt som möjligt.
2. Se till att dina data lagras på ett sätt som du kan utföra aggregeringar som är lämpliga för ditt scenario.
3. Se till att du inte träffar TSDS maximala egenskapen gränser
   - 600 egenskaper (kolumner) för S1 miljöer.
   - 800 egenskaper (kolumner) för S2 miljöer.

Följande riktlinjer hjälper att säkerställa bästa frågeprestanda:

1. Använd inte dynamisk egenskaper, till exempel en tagg-ID som egenskapsnamn, som det bidrar till att träffa gränsen för högsta egenskaper.
2. Skicka inte onödiga egenskaper. Om en fråga egenskap krävs inte är det bäst att inte skicka det och undvika lagring begränsningar.
3. Använd [referensdata](time-series-insights-add-reference-data-set.md), för att undvika att skicka statiska data över nätverket.
4. Dela dimensionsegenskaper bland flera händelser att skicka data via nätverket mer effektivt.
5. Använd inte djupgående matris kapsling. TSD stöder upp till två nivåer av kapslade matriser som innehåller objekt. TSD förenklar matriser i meddelanden, i flera händelser med egenskapen värde-par.
6. Om bara några åtgärder finns för alla eller de flesta händelser, är det bättre att skicka dessa åtgärder som separata egenskaper inom samma objekt. Skicka dem separat minskar antalet händelser och kan göra frågor mer performant färre händelser som behöver bearbetas. När det finns flera åtgärder kan minimerar skickas som värden i en enskild egenskap möjligheten att träffa maximala egenskapen gränsen.

## <a name="examples"></a>Exempel

Följande två exempel visar skickar händelser för att fokusera på rekommendationerna ovan. Följande varje exempel ser du hur rekommendationerna har tillämpats.

Exemplen är baserade på ett scenario där flera enheter skicka mått eller signaler. Mått eller signaler vara flöda hastighet, Oljetryck, temperatur och fuktighet. I det första exemplet att det finns några mått på alla enheter. Det finns många enheter i det andra exemplet och varje skickar många unika mått.

### <a name="scenario-only-a-few-measurementssignals-exist"></a>Scenario: endast några mätningar/signaler finns

**Rekommendation:** skicka varje mått som en separat egenskapskolumnen.

I följande exempel finns ett enda meddelande i IoT-hubb där yttre matrisen innehåller ett delat avsnitt av vanliga värden. Yttre matrisen använder referensdata för att öka effektiviteten för meddelandet. Referensdata innehåller enhetens metadata som inte ändras med varje händelse, men ger användbar egenskaper för dataanalys. Både batchbearbetning vanliga värden och använda referensdata, sparar på byte skickas via kabeln, vilket gör meddelandet mer effektivt.

Exempel JSON-nyttolast:

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

Referenstabell för data (nyckelegenskapen är deviceId):

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | RADEN\_DATA | EU |
| RÅÅÅ | RADEN\_DATA | USA |

Tid serien insikter händelse tabell (efter att förenkla):

| deviceId | messageId | deviceLocation | tidsstämpel | serie. Flödet hastighet ft3/s | serie. Motorn olja trycket psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | RADEN\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | RADEN\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| RÅÅÅ | RADEN\_DATA | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Observera följande i det förra exemplet:

- Den **deviceId** kolumnen fungerar som kolumnrubrik för olika enheter i en. Försök att göra deviceId värdet skulle sin egen egenskapsnamn begränsad Totalt antal enheter 595 (S1 miljöer) eller 795 (S2 miljöer) med fem kolumner.

- Onödiga egenskaper kan undvikas, till exempel information om tillverkare och modell, osv. Eftersom de inte efterfrågas i framtiden, aktiverar eliminera dem bättre nätverks- och lagringseffektiviteten.

- Referensdata används för att minska antalet byte som överförs i nätverket. Två attribut **messageId** och **deviceLocation** , med hjälp av nyckelegenskapen **deviceId**. Dessa data är anslutna med telemetridata vid ingång tidpunkt och därefter lagras i TSD för frågor.

- Två lager med kapsling används, vilket är det högsta tillåtna antalet kapslade som stöds av TSD. Det är viktigt att undvika djupt kapslade matriser.

- Åtgärder som skickas som separata egenskaper inom samma objekt eftersom det inte finns några åtgärder. Här kan **serien. Flöda hastighet psi** och **serien. Motorn olja trycket ft3/s** är unika kolumner.

### <a name="scenario-several-measures-exist"></a>Scenario: flera åtgärder finns

**Rekommendation:** skicka mätningar som ”typ”, ”a”, ”värde” tupplar.

Exempel JSON-nyttolast:

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

Referensdata (nyckelegenskaper är deviceId och series.tagId):

| deviceId | series.tagId | messageId | deviceLocation | typ | enhet |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | RADEN\_DATA | EU | Flöde | ft3/s |
| FXXX | oilPressure | RADEN\_DATA | EU | Oljetryck | psi |
| RÅÅÅ | pumpRate | RADEN\_DATA | USA | Flöde | ft3/s |
| RÅÅÅ | oilPressure | RADEN\_DATA | USA | Oljetryck | psi |

Tid serien insikter händelse tabell (efter att förenkla):

| deviceId | series.tagId | messageId | deviceLocation | typ | enhet | tidsstämpel | Series.Value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | RADEN\_DATA | EU | Flöde | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | RADEN\_DATA | EU | Oljetryck | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | RADEN\_DATA | EU | Flöde | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | RADEN\_DATA | EU | Oljetryck | PSI | 2018-01-17T01:17:00Z | 49.2 |
| RÅÅÅ | pumpRate | RADEN\_DATA | USA | Flöde | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| RÅÅÅ | oilPressure | RADEN\_DATA | USA | Oljetryck | psi | 2018-01-17T01:18:00Z | 22.2 |

Observera följande i föregående exempel, och liknar det första exemplet:

- kolumner **deviceId** och **series.tagId** utgöra kolumnrubriker för olika enheter och taggar i en. Med hjälp av varje som eget attribut skulle begränsad förfrågan till 594 (S1 miljöer) eller 794 (S2 miljöer) totalt antal enheter med sex kolumner.

- onödiga egenskaper har undvikas av skäl som anges i det första exemplet.

- referensdata används för att minska antalet byte som överförs i nätverket genom att introducera **deviceId**, för ett unikt par **messageId** och **deviceLocation**. En sammansatt nyckel används, **series.tagId**, för unikt par med **typen** och **enheten**. Den sammansatta nyckeln tillåter den **deviceId** och **series.tagId** par som ska användas att referera till fyra värden: **messageId deviceLocation, Skriv** och **enhet** . Dessa data är anslutna med telemetridata vid ingång tidpunkt och därefter lagras i TSD för frågor.

- två lager med kapsling används av skäl som anges i det första exemplet.

### <a name="for-both-scenarios"></a>För båda scenarierna

Om du har en egenskap med ett stort antal möjliga värden är det bäst att skicka som distinkta värden i en kolumn, i stället för att skapa en ny kolumn för varje värde. I föregående två exempel:
  - Det finns några egenskaper som har flera värden så att det är lämpligt att var och en separat egenskap i det första exemplet. 
  - Men i det andra exemplet ser du att åtgärderna som inte har angetts som enskilda egenskaper, men i stället en matris med värden/åtgärder under en gemensam egenskap för serien. En ny nyckel som skickas, **tagId** , vilket skapar en ny kolumn, **series.tagId** i tabellen sammanslagna. Nya egenskaper skapas **typen** och **enhet**, med hjälp av referensdata, vilket förhindrar att egenskapen gränsen som träffar.

## <a name="next-steps"></a>Nästa steg

Om du vill placera dessa riktlinjer i praktiken, se [Azure tid serien Insights frågesyntax](/rest/api/time-series-insights/time-series-insights-reference-query-syntax) lära dig mer om frågesyntaxen för TSD dataåtkomst REST API.