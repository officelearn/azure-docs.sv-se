---
title: AZURE Service Fabric CLI- sfctl-behållare
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för behållare.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 07861e2b67eea67740b341cbea994de978973664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906111"
---
# <a name="sfctl-container"></a>sfctl container
Kör behållarrelaterade kommandon på en klusternod.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| anropa-api | Anropa behållar-API på en behållare som distribueras på en tjänst fabric-nod för det angivna kodpaketet. |
| loggar | Hämtar behållarloggarna för behållare som distribueras på en Service Fabric-nod. |

## <a name="sfctl-container-invoke-api"></a>sfctl-behållare invoke-api
Anropa behållar-API på en behållare som distribueras på en tjänst fabric-nod för det angivna kodpaketet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. <br><br> Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --code-package-instance-id [Obligatoriskt] | ID som unikt identifierar en kodpaketinstans som distribueras på en tjänstinfrastrukturnod. <br><br> Kan hämtas med "servicekod-paket-lista". |
| --kod-paket-namn [Obligatoriskt] | Namnet på kodpaketet som anges i tjänstmanifestet som registrerats som en del av en programtyp i ett Service Fabric-kluster. |
| --container-api-uri-path [Obligatoriskt] | Rest-uri-sökväg för behållare, använd {ID} i stället för behållarnamn/id. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --service-manifest-namn [Obligatoriskt] | Namnet på ett tjänstmanifest som registrerats som en del av en programtyp i ett Service Fabric-kluster. |
| --container-api-body | HTTP-begärandetext för REST API för behållare. |
| --container-api-content-type | Innehållstyp för REST API för behållare, som standard "application/json". |
| --container-api-http-verb | HTTP-verb för rest-API för behållare, som standard get. |
| --timeout -t | Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-container-logs"></a>sfctl behållarloggar
Hämtar behållarloggarna för behållare som distribueras på en Service Fabric-nod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. <br><br> Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --code-package-instance-id [Obligatoriskt] | Kodpaketinstans-ID, som kan hämtas med "servicekod-paket-lista". |
| --kod-paket-namn [Obligatoriskt] | Namnet på kodpaketet som anges i tjänstmanifestet som registrerats som en del av en programtyp i ett Service Fabric-kluster. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --service-manifest-namn [Obligatoriskt] | Namnet på ett tjänstmanifest som registrerats som en del av en programtyp i ett Service Fabric-kluster. |
| --svans | Antal rader som ska visas från slutet av loggarna. Standard är 100. "alla" för att visa hela loggarna. |
| --timeout -t | Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).