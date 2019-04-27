---
title: Azure Service Fabric CLI - sfctl nät nätverk | Microsoft Docs
description: Beskriver nätverkskommandon för Service Fabric CLI sfctl nät.
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
ms.openlocfilehash: feec5c4796c025c1707b4eb93bfe34b8d384ef3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694529"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Hämta och ta bort nät nätverksresurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort nätverksresursen. |
| lista | Visar en lista över alla nätverksresurser. |
| visa | Hämtar nätverksresursen med det angivna namnet. |

## <a name="sfctl-mesh-network-delete"></a>sfctl nät nätverk delete
Tar bort nätverksresursen.

Tar bort den nätverksresurs som identifieras av namnet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --name -n [krävs] | Namnet på nätverket. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-mesh-network-list"></a>listan över sfctl nät nätverk
Visar en lista över alla nätverksresurser.

Hämtar information om alla nätverksresurser i en viss resursgrupp. Informationen omfattar beskrivningen och andra egenskaper för nätverket.

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-mesh-network-show"></a>sfctl nät nätverk show
Hämtar nätverksresursen med det angivna namnet.

Hämtar information om nätverksresursen med det angivna namnet. Informationen omfattar beskrivningen och andra egenskaper för nätverket.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --name -n [krävs] | Namnet på nätverket. |

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