---
title: Azure Service Fabric CLI - sfctl nät service och repliken | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl nät tjänsterepliken kommandon.
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
ms.openlocfilehash: bcf4b8d013783a9fbdb62bcdb8737680bfce7640
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285090"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl nät service-replik
Få information om repliker och lista replikeringar av en viss tjänst i en resurs för en.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| lista | Visar en lista över alla repliker av en tjänst. |
| Visa | Hämtar den angivna repliken av tjänsten för ett program. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl nät tjänsterepliken lista
Visar en lista över alla repliker av en tjänst.

Hämtar information om alla repliker av en tjänst. Informationen omfattar beskrivningen och andra egenskaper för tjänsterepliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --appens namn---programnamn [krävs] | Namnet på programmet. |
| --Tjänstnamn [krävs] | Namnet på tjänsten. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl nät tjänsterepliken show
Hämtar den angivna repliken av tjänsten för ett program.

Hämtar information om tjänsterepliken med det angivna namnet. Informationen omfattar beskrivningen och andra egenskaper för tjänsterepliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --appens namn---programnamn [krävs] | Namnet på programmet. |
| --name -n [krävs] | Namnet på tjänsterepliken. |
| --Tjänstnamn [krävs] | Namnet på tjänsten. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).