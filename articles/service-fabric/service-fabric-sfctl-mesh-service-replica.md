---
title: Azure Service Fabric CLI- sfctl mesh service-replik
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för att hämta replikinformation för ett programresurser.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ac604ec0a854964746ef34627b054e853662265f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905950"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Hämta replikinformation och lista repliker av en viss tjänst i en programresurs.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| lista | Visar alla repliker av en tjänst. |
| visa | Hämtar den angivna repliken av tjänsten för ett program. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh tjänst-repliklista
Visar alla repliker av en tjänst.

Hämtar information om alla repliker av en tjänst. Informationen innehåller beskrivningen och andra egenskaper för tjänstrepliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-name --application-name [Obligatoriskt] | Namnet på programmet. |
| --service-name [Obligatoriskt] | Namnet på tjänsten. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replika visa
Hämtar den angivna repliken av tjänsten för ett program.

Hämtar information om tjänstrepliken med förnamnet. Informationen innehåller beskrivningen och andra egenskaper för tjänstrepliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-name --application-name [Obligatoriskt] | Namnet på programmet. |
| --namn -n [Obligatoriskt] | Namnet på tjänstrepliken. |
| --service-name [Obligatoriskt] | Namnet på tjänsten. |

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