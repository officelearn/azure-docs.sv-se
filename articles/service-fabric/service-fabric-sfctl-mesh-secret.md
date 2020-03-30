---
title: Azure Service Fabric CLI- sfctl mesh hemlighet
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för att hämta och ta bort hemliga resurser för Service Fabric Mesh.Includes a list of commands for getting and borttagning Service Fabric Mesh secret resources.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fab388ff223eb95020e2ba0945c76532bc54f224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905979"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Hämta och ta bort hemliga resurser för mesh.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort den hemliga resursen. |
| lista | Visar alla hemliga resurser. |
| visa | Hämtar den hemliga resursen med förnamnet. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl mesh hemlig bort
Tar bort den hemliga resursen.

Tar bort den angivna hemliga resursen och alla dess namngivna värden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --namn -n [Obligatoriskt] | Namnet på den hemliga resursen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-mesh-secret-list"></a>sfctl mesh hemlig lista
Visar alla hemliga resurser.

Hämtar information om alla hemliga resurser i en viss resursgrupp. Informationen innehåller beskrivningen och andra egenskaper för hemligheten.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-mesh-secret-show"></a>sfctl mesh hemlig show
Hämtar den hemliga resursen med förnamnet.

Hämtar information om den hemliga resursen med förnamnet. Informationen innehåller beskrivningen och andra egenskaper för hemligheten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --namn -n [Obligatoriskt] | Namnet på den hemliga resursen. |

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