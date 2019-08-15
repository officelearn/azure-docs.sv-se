---
title: Azure Service Fabric CLI – sfctl nät distribution | Microsoft Docs
description: Beskriver distributions kommandon Service Fabric för sfctl CLI-.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b3f506b46ef563f47fc7c67b759d3fcd08b7509d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035177"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Skapa Service Fabric nät resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| create | Skapar en distribution av Service Fabric nät resurser. |

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
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
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

Konsoliderar och distribuerar alla resurser i en katalog till kluster genom att åsidosätta parametrarna, som skickas direkt som JSON-objekt

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).