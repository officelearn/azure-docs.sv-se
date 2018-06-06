---
title: Azure Service Fabric CLI - sfctl chaos schema | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl chaos schema-kommandon.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 0d09338f71d71d07ab0e037d4736cfaa1f3cff85
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764034"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schema
Hämta och Schemalägg chaos.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Hämta | Hämta Chaos schemat definierar när och hur du kör Chaos. |
| Ange | Ange ett Chaos schema som ska användas av Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>Hämta sfctl chaos schema
Hämta Chaos schemat definierar när och hur du kör Chaos.

Hämtar versionen av schemat Chaos används och Chaos schemat som definierar när och hur du kör Chaos.

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schema har angivits
Ange ett Chaos schema som ska användas av Chaos.

Ange ett schema för Chaos för närvarande används av Chaos. Chaos schemalägger automatiskt baserat på schemat Chaos körs. Versionen i angivna indata schemat måste matcha versionen av schemat Chaos på servern. Om den version som inte matchar versionen på servern, uppdateras inte Chaos schemat. Om den version som matchar versionen på servern, Chaos schemat uppdateras och versionen av Chaos schemat på servern ökas upp med ett och radbryter till 0 när 2 147 483 647. Om Chaos körs när det här anropet görs, kommer anropet att misslyckas.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --chaos-parametrar-ordlista | JSON-kodade lista som representerar en mappning av strängen namn för ChaosParameters som ska användas av jobb. |
| --utc-datum-upphör att gälla | Datum och tid när du vill sluta använda schemat för att schemalägga Chaos.  Som standard\: 9999-12-31T23\:59\:59.999Z. |
| --jobb | JSON-kodade lista över ChaosScheduleJobs som representerar när du ska köra Chaos och med vilka parametrar som ska köra Chaos med. |
| --utc-datum-start | Datum och tid när du vill börja använda schemat för att schemalägga Chaos.  Som standard\: 1601-01-01T00\:00\:00.000Z. |
| --version | Versionsnummer för schemat. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

### <a name="examples"></a>Exempel

Följande kommando anger ett schema som börjar på 2016-01-01 och upphör att gälla 2038-01-01 som kör Chaos 24 timmar på dagen, 7 dagar i veckan (under förutsättning att det aktuella schemat har version 0). Chaos kommer att schemaläggas i klustret för den tidsperioden.

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
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).