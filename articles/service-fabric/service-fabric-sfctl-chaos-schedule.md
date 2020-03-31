---
title: Azure Service Fabric CLI- sfctl kaos schema
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för kaosplanering.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 29b365c48e405830e238945f1d94156f477c15b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906180"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Ställ in kaosschemat.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| get | Få Chaos Schedule definiera när och hur man kör Chaos. |
| set | Ange schemat som används av Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl kaos schema få
Få Chaos Schedule definiera när och hur man kör Chaos.

Hämtar den version av Chaos Schedule som används och Chaos Schedule som definierar när och hur du kör Chaos.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl kaos schema som
Ange schemat som används av Chaos.

Kaos schemaläggs automatiskt körs baserat på Chaos Schedule. Chaos-schemat uppdateras om den angivna versionen matchar versionen på servern. När kaosschemat uppdateras ökas versionen på servern med 1. Versionen på servern kommer att slå tillbaka till 0 efter att ha nått ett stort antal. Om Chaos körs när det här samtalet görs misslyckas samtalet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kaos-parametrar-ordbok | JSON kodad lista som representerar en mappning av strängnamn till ChaosParameters som ska användas av jobb. |
| --utgång-datum-utc | Datum och tid för när du ska sluta använda schemat för att schemalägga Kaos.  Standard\: 9999-12-31T23\:59\:59.999Z. |
| --jobb | JSON kodade lista över ChaosScheduleJobs som representerar när chaos ska köras och med vilka parametrar som ska köras Kaos med. |
| --start-date-utc -start-date-utc --start-date-utc --start | Datum och tid för när du ska börja använda schemat för att schemalägga Kaos.  Standard\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Standard\: 60. |
| --version | Versionsnumret för schemat. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

### <a name="examples"></a>Exempel

Följande kommando anger ett schema (förutsatt att det aktuella schemat har version 0) som börjar 2016-01-01 och löper ut 2038-01-01 som kör Chaos 24 timmar om dygnet, 7 dagar i veckan.
Kaos kommer att schemaläggas i klustret för den tiden.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).
