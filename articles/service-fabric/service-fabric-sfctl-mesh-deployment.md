---
title: Azure Service Fabric CLI – sfctl nät distribution | Microsoft Docs
description: Beskriver distributions kommandon Service Fabric för sfctl CLI-.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: dbecf8e96c6cb5d0d6d873309d848304fe33eaaa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901258"
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