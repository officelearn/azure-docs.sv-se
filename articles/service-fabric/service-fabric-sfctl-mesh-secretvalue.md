---
title: Azure Service Fabric CLI- sfctl mesh secretvalue
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för att hämta och ta bort secretvalue-resurser för Service Fabric Mesh.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b2fe3a7995cdd88a8f63d0d865e72e9f161cf9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905959"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Hämta och ta bort resurser för hemligvärde av nät.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort det angivna värdet för den namngivna hemliga resursen. |
| lista | Lista namn på alla värden för den angivna hemliga resursen. |
| visa | Visar det angivna värdet för den hemliga resursen. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete sfctl mesh secretvalue delete sfctl mesh secretvalue delete sfc
Tar bort det angivna värdet för den namngivna hemliga resursen.

Tar bort den hemliga värderesurs som identifieras med namnet. Namnet på resursen är vanligtvis den version som är associerad med det värdet. Borttagningen misslyckas om det angivna värdet används.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --hemligt namn -n [Obligatoriskt] | Namnet på den hemliga resursen. |
| --version -v [Obligatoriskt] | Namnet på den hemliga versionen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue lista
Lista namn på alla värden för den angivna hemliga resursen.

Hämtar information om alla hemliga värderesurser för den angivna hemliga resursen. Informationen innehåller namnen på de hemliga värderesurserna, men inte de faktiska värdena.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --hemligt namn -n [Obligatoriskt] | Namnet på den hemliga resursen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Visar det angivna värdet för den hemliga resursen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --hemligt namn -n [Obligatoriskt] | Namnet på den hemliga resursen. |
| --version -v [Obligatoriskt] | Namnet på den hemliga versionen. |
| --show-värde | Visa det hemliga versionens faktiska värde. |

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