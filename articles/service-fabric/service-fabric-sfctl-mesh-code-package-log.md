---
title: Azure Service Fabric CLI- sfctl mesh kod-paket-logg
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista över kommandon för att hämta loggar för ett angivet kodpaket.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906044"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Hämta loggarna för behållaren för det angivna kodpaketet för den angivna tjänstrepliken.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| get | Hämtar loggarna från behållaren. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh kod-paket-logg få
Hämtar loggarna från behållaren.

Hämtar loggarna för behållaren för det angivna kodpaketet för tjänstrepliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-name --application-name [Obligatoriskt] | Namnet på programmet. |
| --kod-paket-namn [Obligatoriskt] | Namnet på tjänstens kodpaket. |
| --repliknamn [Obligatoriskt] | Repliknamn för Service Fabric. |
| --service-name [Obligatoriskt] | Namnet på tjänsten. |
| --svans | Antal rader som ska visas från slutet av loggarna. Standard är 100. "alla" för att visa hela loggarna. |

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