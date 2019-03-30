---
title: Azure Service Fabric CLI - sfctl store | Microsoft Docs
description: Beskriver sfctl store-kommandon för Service Fabric CLI.
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
ms.openlocfilehash: 65dcceb2e55ec0927630b32670d2f915a01903bf
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667955"
---
# <a name="sfctl-store"></a>sfctl store
Utföra grundläggande nivå filåtgärder på avbildningsarkivet kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort befintliga bild lagra innehållet. |
| root-info | Hämtar innehållsinformationen i avbildningsarkivet roten. |
| stat | Hämtar innehåll bildinformation store. |

## <a name="sfctl-store-delete"></a>sfctl store delete
Tar bort befintliga bild lagra innehållet.

Tar bort befintliga store innehållet hittas inom den angivna avbildningen lagra relativ sökväg. Det här kommandot kan användas för att ta bort överförda programpaket när de har etablerats.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --innehåll-path [krävs] | Relativ sökväg till filen eller mappen i avbildningsarkivet från dess rot. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-store-root-info"></a>sfctl store rot-info
Hämtar innehållsinformationen i avbildningsarkivet roten.

Returnerar information om avbildningsarkivet innehåll i roten på avbildningsarkivet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-store-stat"></a>sfctl store stat
Hämtar innehåll bildinformation store.

Returnerar information om store innehållet i på den angivna contentPath. ContentPath är i förhållande till roten för avbildningsarkivet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --innehåll-path [krävs] | Relativ sökväg till filen eller mappen i avbildningsarkivet från dess rot. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).