---
title: Azure Service Fabric CLI- sfctl mesh-tjänst
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för att hämta serviceinformation för en programresurs.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905935"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Hämta serviceinformation och listtjänster för en programresurs.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| lista | Visar alla tjänstresurser. |
| visa | Hämtar serviceresursen med förnamnet. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh tjänstlista
Visar alla tjänstresurser.

Hämtar information om alla tjänster för en programresurs. Informationen innehåller tjänstens beskrivning och andra egenskaper.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-name --application-name [Obligatoriskt] | Namnet på programmet. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
Hämtar serviceresursen med förnamnet.

Hämtar information om serviceresursen med förnamnet. Informationen innehåller tjänstens beskrivning och andra egenskaper.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-name --application-name [Obligatoriskt] | Namnet på programmet. |
| --namn -n [Obligatoriskt] | Namnet på tjänsten. |

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