---
title: Azure Service Fabric CLI – sfctl kaos-schema | Microsoft Docs
description: Beskriver schema kommandona för Service Fabric CLI-sfctl kaos.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 1664978110b7c700906cbf4e6c80806ac70f1f05
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036530"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Hämta och ange kaos-schemat.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Hämta | Hämta kaos-schemat som definierar när och hur du ska köra kaos. |
| set | Ange det schema som används av kaos. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl kaos schema get
Hämta kaos-schemat som definierar när och hur du ska köra kaos.

Hämtar den version av kaos-schemat som används och kaos-schemat som definierar när och hur du kör kaos.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl kaos schema uppsättning
Ange det schema som används av kaos.

Kaos schemalägger automatiskt körningar baserat på kaos-schemat. Versionen i det angivna schemat måste matcha versionen av kaos-schemat på servern. Om den angivna versionen inte matchar versionen på servern, uppdateras inte kaos-schemat. Om den version som tillhandahöll matchar versionen på servern, uppdateras kaos-schemat och versionen av kaos-schemat på servern ökas med en och omsluts tillbaka till 0 efter 2 147 483 647. Om kaos körs när det här anropet görs fungerar inte anropet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kaos-Parameters-Dictionary | JSON-kodad lista som representerar en mappning av sträng namn till ChaosParameters som ska användas av jobb. |
| --expiry-date-utc | Datum och tid för när du ska sluta använda schemat för att schemalägga kaos.  Standard\: 9999-12-31T23\:59\:59.999 z. |
| – jobb | JSON-kodad lista över ChaosScheduleJobs som representerar när du ska köra kaos och med vilka parametrar som kaos ska köras med. |
| --start-date-utc | Datum och tid för när du ska börja använda schemat för att schemalägga kaos.  Standard\: 1601 – 01-01T00\:00\:00.000 z. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |
| --version | Schemats versions nummer. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

### <a name="examples"></a>Exempel

Följande kommando anger ett schema (förutsatt att det aktuella schemat har version 0) som börjar på 2016-01-01 och upphör att gälla 2038-01-01 som kör kaos 24 timmar på dagen, 7 dagar i veckan. Kaos kommer att schemaläggas för klustret för den tiden.

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
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).
