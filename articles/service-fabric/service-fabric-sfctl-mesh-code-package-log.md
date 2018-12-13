---
title: Azure Service Fabric CLI - sfctl nät code-package-log | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl nät code-package-log-kommandon.
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
ms.openlocfilehash: 81ddcc8c5685a839afabc1e82ecf4246cb813c21
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285097"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl nät code-package-log
Hämta loggarna för behållaren på angivna koden paketet för den angivna tjänsterepliken.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Hämta | Hämtar loggarna från behållaren. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl nät code-package-log get
Hämtar loggarna från behållaren.

Hämtar loggarna för den angivna koden paketet av tjänsterepliken behållaren.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --appens namn---programnamn [krävs] | Namnet på programmet. |
| --code-package-name [krävs] | Namnet på kodpaketet i tjänsten. |
| ---namn [krävs] | Service Fabric namn. |
| --Tjänstnamn [krävs] | Namnet på tjänsten. |
| --pilslut | Antal rader som ska visas från slutet av loggarna. Standardvärdet är 100. ”alla” för att visa de fullständiga loggarna. |

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