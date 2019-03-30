---
title: Azure Service Fabric CLI - sfctl nät code-package-log | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl nät code-package-log-kommandon.
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
ms.openlocfilehash: e7bc8491071946eaa2e322517e5d36d681a49130
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661079"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
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