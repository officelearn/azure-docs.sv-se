---
title: Azure Service Fabric CLI – sfctl nät distribution
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att skapa Service Fabric nät resurser.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 6512cce075906fc8708a39fa0747ec27be33e961
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645369"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Skapa Service Fabric nät resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar en distribution av Service Fabric nät resurser. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl nät distribution skapa
Skapar en distribution av Service Fabric nät resurser.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --yaml-Files [required] | Kommaavgränsade relativa/absoluta fil Sök vägar för alla yaml-filer eller relativa/absoluta sökvägen till katalogen (rekursivt) som innehåller yaml-filer. |
| --parametrar | En relativ/absolut sökväg till yaml-fil eller ett JSON-objekt som innehåller de parametrar som behöver åsidosättas. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

### <a name="examples"></a>Exempel

Konsoliderar och distribuerar alla resurser till klustret genom att åsidosätta de parametrar som anges i yaml-filen
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Konsoliderar och distribuerar alla resurser i en katalog till kluster genom att åsidosätta de parametrar som anges i yaml-filen

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliderar och distribuerar alla resurser i en katalog till kluster genom att åsidosätta parametrarna som skickas direkt som JSON-objekt
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).