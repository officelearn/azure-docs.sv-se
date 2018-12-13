---
title: Azure Service Fabric CLI - sfctl-nät-distribution | Microsoft Docs
description: Beskriver kommandona Service Fabric CLI för distribution av sfctl-nät.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b25384d8f3c6e41b6c5cca723d41b79f00b17494
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285447"
---
# <a name="sfctl-mesh-deployment"></a>sfctl nät distribution
Skapa Service Fabric blandar resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa | Skapar en distribution av Service Fabric-nät resurser. |

## <a name="sfctl-mesh-deployment-create"></a>Skapa sfctl nät distribution
Skapar en distribution av Service Fabric-nät resurser.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --input-yaml-filer [krävs] | CSV-relativ/absolut sökvägar till alla yaml-filer eller relativ/absoluta sökvägen till katalogen (rekursiv) som innehåller yaml-filer. |
| – parametrar | En relativ/absolut sökväg till yaml-fil eller ett json-objekt som innehåller parametrar som ska åsidosättas. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

### <a name="examples"></a>Exempel

Konsoliderar och distribuerar alla resurser som ska ingå i klustret genom att åsidosätta de parametrar som anges i yaml-fil

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Konsoliderar och distribuerar alla resurser i en katalog till klustret genom att åsidosätta de parametrar som anges i yaml-fil

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliderar och distribuerar alla resurser i en katalog till klustret genom att åsidosätta de parametrar som skickas direkt som json-objekt

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).