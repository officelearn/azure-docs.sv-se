---
title: Azure Service Fabric CLI - sfctl nät service och repliken | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl nät tjänsterepliken kommandon.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 1e0955ef2a52a6313f0449b956229e03f0a5b5e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038456"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Få information om repliker och lista replikeringar av en viss tjänst i en resurs för en.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| lista | Visar en lista över alla repliker av en tjänst. |
| visa | Hämtar den angivna repliken av tjänsten för ett program. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl nät tjänsterepliken lista
Visar en lista över alla repliker av en tjänst.

Hämtar information om alla repliker av en tjänst. Informationen omfattar beskrivningen och andra egenskaper för tjänsterepliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --appens namn---programnamn [krävs] | Namnet på programmet. |
| --Tjänstnamn [krävs] | Namnet på tjänsten. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
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

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).