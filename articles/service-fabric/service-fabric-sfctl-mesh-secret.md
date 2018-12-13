---
title: Azure Service Fabric CLI - sfctl nät secret | Microsoft Docs
description: Beskriver de hemliga kommandona för Service Fabric CLI sfctl nät.
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
ms.openlocfilehash: e2dc9f815fc578bbe1f01b6b64327ccf23a1aebb
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285132"
---
# <a name="sfctl-mesh-secret"></a>sfctl nät hemlighet
Hämta och ta bort nät hemliga resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort den hemliga resursen. |
| lista | Visar en lista över alla hemliga resurser. |
| Visa | Hämtar den hemliga resursen med det angivna namnet. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl nät secret delete
Tar bort den hemliga resursen.

Tar bort den angivna hemliga resursen och alla dess namngivna värden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --name -n [krävs] | Namnet på hemligheten. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-mesh-secret-list"></a>sfctl nät secret list
Visar en lista över alla hemliga resurser.

Hämtar information om alla hemliga resurser i en viss resursgrupp. Informationen omfattar beskrivningen och andra egenskaper för hemlighet.

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-mesh-secret-show"></a>sfctl nät secret show
Hämtar den hemliga resursen med det angivna namnet.

Hämtar information om den hemliga resursen med det angivna namnet. Informationen omfattar beskrivningen och andra egenskaper för hemlighet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --name -n [krävs] | Namnet på hemligheten. |

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