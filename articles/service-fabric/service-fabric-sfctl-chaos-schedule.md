---
title: Azure Service Fabric CLI - sfctl chaos schedule | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl chaos schema-kommandon.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: dc3dd06b5feac1f66598cd65fa79f447a1bbd9be
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668321"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Hämta och Schemalägg chaos.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Hämta | Hämta Chaos-schemat som definierar när och hur du kör Chaos. |
| set | Ange det schema som används av Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>Hämta sfctl chaos schema
Hämta Chaos-schemat som definierar när och hur du kör Chaos.

Hämtar versionen av Chaos-schema används och Chaos schemat som definierar när och hur du kör Chaos.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schema har angivits
Ange det schema som används av Chaos.

Chaos kommer automatiskt att schemalägga körningar baserat på schemat Chaos. Versionen i angivna indata schemat måste matcha versionen av Chaos schemat på servern. Om den version som följde inte matchar versionen på servern, uppdateras inte Chaos-schema. Om den version som följde matchar versionen på servern, Chaos-schemat uppdateras och versionen av Chaos schemat på servern ökas upp med ett och radbryts till 0 efter 2 147 483 647. Om Chaos körs när det här anropet görs att anropet misslyckas.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --chaos-parameters-dictionary | JSON-kodad lista som representerar en mappning av strängen namn för ChaosParameters som ska användas av jobb. |
| --expiry-date-utc | Datum och tid när du vill sluta använda schemat för att schemalägga Chaos.  Default\: 9999-12-31T23\:59\:59.999Z. |
| --jobb | JSON-kodad lista över ChaosScheduleJobs som representerar när du ska köra Chaos och med vilka parametrar som ska köra Chaos med. |
| --start-date-utc | Datum och tid när du vill börja använda schemat för att schemalägga Chaos.  Standard\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --version | Versionsnummer för schemat. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

### <a name="examples"></a>Exempel

Följande kommando anger ett schema som startar på 2016-01-01 och upphör att gälla 2038-01-01 som kör Chaos 24 timmar på dagen, 7 dagar i veckan (förutsatt att det aktuella schemat har version 0). Chaos schemaläggs på klustret för den tiden.

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


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
