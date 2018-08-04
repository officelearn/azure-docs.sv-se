---
title: Azure Service Fabric CLI - sfctl container | Microsoft Docs
description: Beskriver sfctl container-kommandon för Service Fabric CLI.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 27108d27ee27346e4cba44e6778faff56df70a36
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495136"
---
# <a name="sfctl-container"></a>sfctl container
Kör behållaren relaterade kommandon på en klusternod.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| anropa api | Anropa behållare REST API. |
| loggar | Hämta behållarloggarna. |

## <a name="sfctl-container-invoke-api"></a>sfctl behållaren anropa API: er
Anropa behållare REST API.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Programidentitet. |
| --code-package-instans-id [krävs] | Koda paketet instans-ID, som kan hämtas efter ”service code-package-list”. |
| --code-package-name [krävs] | Kod paketnamn. |
| --container-api-uri-sökväg [krävs] | Behållaren REST API-URI-sökväg, Använd {id} i stället för behållar-namn/id. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --manifest-tjänstnamnet [krävs] | Namn på manifest. |
| ---api-behållaren | HTTP-begärandetexten för REST API-behållare. |
| --container-api-content-type | Innehållstyp för REST API-behållare som standard ”application/json”. |
| --container-api-http-verb | HTTP-verb för REST API-behållare som standard GET. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-container-logs"></a>sfctl behållarloggar
Hämta behållarloggarna.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Programidentitet. |
| --code-package-instans-id [krävs] | Koda paketet instans-ID, som kan hämtas efter ”service code-package-list”. |
| --code-package-name [krävs] | Kod paketnamn. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --manifest-tjänstnamnet [krävs] | Namn på manifest. |
| --pilslut | Returnera endast log rader i från slutet av loggarna. Ange som ett heltal eller alla för att mata ut alla rader i loggen. Som standard för alla. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).