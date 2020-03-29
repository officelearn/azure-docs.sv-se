---
title: Azure Service Fabric CLI- sfctl mesh-distribution
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för att skapa Service Fabric Mesh-resurser.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906038"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Skapa service fabric mesh-resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar en distribution av Service Fabric Mesh-resurser. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh-distribution skapa
Skapar en distribution av Service Fabric Mesh-resurser.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --input-yaml-files [Obligatoriskt] | Kommaavgränsade relativa eller absoluta filsökvägar för alla yaml-filer eller den relativa eller absoluta sökvägen till katalogen (rekursiv) som innehåller yaml-filer. |
| --parametrar | En relativ eller absolut sökväg till en yaml-fil eller ett json-objekt som innehåller de parametrar som måste åsidosättas. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

### <a name="examples"></a>Exempel

Konsoliderar och distribuerar alla resurser till kluster genom att åsidosätta de parametrar som nämns i yaml-filen
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Konsoliderar och distribuerar alla resurser i en katalog till kluster genom att åsidosätta de parametrar som nämns i yaml-filen

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliderar och distribuerar alla resurser i en katalog till kluster genom att åsidosätta de parametrar som skickas direkt som json-objekt
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).
