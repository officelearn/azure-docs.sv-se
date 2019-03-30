---
title: Azure Service Fabric CLI - sfctl container | Microsoft Docs
description: Beskriver sfctl container-kommandon för Service Fabric CLI.
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
ms.openlocfilehash: a5037c535737946a50d8af6fa60d0815120276d9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663714"
---
# <a name="sfctl-container"></a>sfctl container
Kör behållaren relaterade kommandon på en klusternod.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| invoke-api | Anropa behållare API i en behållare som distribueras på en Service Fabric-nod för det givna code-paketet. |
| loggar | Hämtar behållarloggarna för behållare som har distribuerats på en Service Fabric-nod för det givna code-paketet. |

## <a name="sfctl-container-invoke-api"></a>sfctl behållaren anropa API: er
Anropa behållare API i en behållare som distribueras på en Service Fabric-nod för det givna code-paketet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. <br><br> Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --code-package-instans-id [krävs] | ID som unikt identifierar en kod paket-instans som distribuerats på en service fabric-noden. <br><br> Kan hämtas efter ”service code-package-list”. |
| --code-package-name [krävs] | Namnet på kodpaketet som anges i tjänstmanifestet registrerad som en del av en typ av program i ett Service Fabric-kluster. |
| --container-api-uri-sökväg [krävs] | Behållaren REST API-URI-sökväg, Använd {id} i stället för behållar-namn/id. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --manifest-tjänstnamnet [krävs] | Namnet på ett tjänstmanifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --container-api-body | HTTP-begärandetexten för REST API-behållare. |
| --container-api-content-type | Innehållstyp för REST API-behållare som standard ”application/json”. |
| --container-api-http-verb | HTTP-verb för REST API-behållare som standard GET. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-container-logs"></a>sfctl behållarloggar
Hämtar behållarloggarna för behållare som har distribuerats på en Service Fabric-nod för det givna code-paketet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. <br><br> Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --code-package-instans-id [krävs] | Koda paketet instans-ID, som kan hämtas efter ”service code-package-list”. |
| --code-package-name [krävs] | Namnet på kodpaketet som anges i tjänstmanifestet registrerad som en del av en typ av program i ett Service Fabric-kluster. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --manifest-tjänstnamnet [krävs] | Namnet på ett tjänstmanifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --pilslut | Antal rader som ska visas från slutet av loggarna. Standardvärdet är 100. ”alla” för att visa de fullständiga loggarna. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).