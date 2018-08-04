---
title: Azure Service Fabric CLI - sfctl store | Microsoft Docs
description: Beskriver sfctl store-kommandon för Service Fabric CLI.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 2f2da5b38ab39e3636ff0ac09ac2c5515e02b0c1
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492343"
---
# <a name="sfctl-store"></a>sfctl store
Utföra grundläggande nivå filåtgärder på avbildningsarkivet kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort befintliga bild lagra innehållet. |
| rot-info | Hämtar innehållsinformationen i avbildningsarkivet roten. |
| stat | Hämtar innehåll bildinformation store. |

## <a name="sfctl-store-delete"></a>sfctl store delete
Tar bort befintliga bild lagra innehållet.

Tar bort befintliga store innehållet hittas inom den angivna avbildningen lagra relativ sökväg. Detta kan användas för att ta bort överförda programpaket när de har etablerats.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --innehåll-path [krävs] | Relativ sökväg till filen eller mappen i avbildningsarkivet från dess rot. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-store-root-info"></a>sfctl store rot-info
Hämtar innehållsinformationen i avbildningsarkivet roten.

Returnerar information om avbildningsarkivet innehåll i roten på avbildningsarkivet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-store-stat"></a>sfctl store stat
Hämtar innehåll bildinformation store.

Returnerar information om store innehållet i på den angivna contentPath. ContentPath är i förhållande till roten för avbildningsarkivet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --innehåll-path [krävs] | Relativ sökväg till filen eller mappen i avbildningsarkivet från dess rot. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).