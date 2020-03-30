---
title: Azure Service Fabric CLI- sfctl mesh gateway
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för att hämta och ta bort gatewayresurser för Service Fabric Mesh.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3e398ff8a1a0a28fd9d0650a7c9d18ba04817d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906012"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Hämta och ta bort nätgatewayresurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort gatewayresursen. |
| lista | Visar alla gatewayresurser. |
| visa | Hämtar gatewayresursen med förnamnet. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl mesh gateway ta bort
Tar bort gatewayresursen.

Tar bort gatewayresursen som identifieras med namnet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --namn -n [Obligatoriskt] | Namnet på gatewayresursen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh gateway lista
Visar alla gatewayresurser.

Hämtar information om alla gatewayresurser i en viss resursgrupp. Informationen innehåller beskrivningen och andra egenskaper för gatewayen.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl mesh gateway show sfctl mesh gateway show sfctl mesh gateway show sfc
Hämtar gatewayresursen med förnamnet.

Hämtar information om gatewayresursen med förnamnet. Informationen innehåller beskrivningen och andra egenskaper för gatewayen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --namn -n [Obligatoriskt] | Namnet på gatewayresursen. |

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