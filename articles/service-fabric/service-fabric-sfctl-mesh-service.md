---
title: Azure Service Fabric CLI sfctl nät tjänst | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl nät service kommandon.
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
ms.openlocfilehash: 6c40cf2340568e704a5c76ad367c07c85826943b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284803"
---
# <a name="sfctl-mesh-service"></a>sfctl nät service
Få information om tjänsten och listan över tjänster på en resurs för en.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| lista | Visar en lista över alla serviceresurser. |
| Visa | Hämtar tjänstresurs med det angivna namnet. |

## <a name="sfctl-mesh-service-list"></a>sfctl nät Tjänstlista
Visar en lista över alla serviceresurser.

Hämtar information om alla tjänster för en resurs för en. Informationen omfattar beskrivningen och andra egenskaper för tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --appens namn---programnamn [krävs] | Namnet på programmet. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-mesh-service-show"></a>sfctl nät service show
Hämtar tjänstresurs med det angivna namnet.

Hämtar information om tjänst-resurs med det angivna namnet. Informationen omfattar beskrivningen och andra egenskaper för tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --appens namn---programnamn [krävs] | Namnet på programmet. |
| --name -n [krävs] | Namnet på tjänsten. |

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