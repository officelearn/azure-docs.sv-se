---
title: Azure Service Fabric CLI - sfctl nät app | Microsoft Docs
description: Beskriver appkommandon för Service Fabric CLI sfctl nät.
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
ms.openlocfilehash: 3c2194f6a001e4fc49dcf1694f8a9cda41550ace
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284838"
---
# <a name="sfctl-mesh-app"></a>sfctl nät app
Hämta och ta bort programresurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort programresursen. |
| lista | Visar en lista över alla resurser som programmet. |
| Visa | Hämtar programresurs med det angivna namnet. |

## <a name="sfctl-mesh-app-delete"></a>sfctl nät app delete
Tar bort programresursen.

Tar bort programresurs som identifieras av namnet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --name -n [krävs] | Namnet på programmet. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-mesh-app-list"></a>sfctl nät app-lista
Visar en lista över alla resurser som programmet.

Hämtar information om alla resurser i en viss resursgrupp. Informationen omfattar beskrivningen och andra egenskaper för programmet.

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-mesh-app-show"></a>sfctl nät app show
Hämtar programresurs med det angivna namnet.

Hämtar information om program-resurs med det angivna namnet. Informationen omfattar beskrivningen och andra egenskaper för programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --name -n [krävs] | Namnet på programmet. |

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