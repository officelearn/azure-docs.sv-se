---
title: Azure Service Fabric CLI - sfctl store | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl store-kommandon.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 39ecf568c5c41c0007b358670af755be1dd5d99e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763246"
---
# <a name="sfctl-store"></a>sfctl store
Utföra grundläggande nivån filåtgärder på klustret image store.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| radera | Tar bort befintliga avbildningen lagra innehållet. |
| rot-info | Hämtar innehållsinformationen i roten för image store. |
| stat | Hämtar innehållsinformation för image store. |

## <a name="sfctl-store-delete"></a>ta bort sfctl-butik
Tar bort befintliga avbildningen lagra innehållet.

Tar bort befintliga image store innehåll som hittas i angivna bilden lagra relativ sökväg. Det här kommandot kan användas för att ta bort överförda programpaket när de har etablerats.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --innehåll sökväg [krävs] | Relativ sökväg till filen eller mappen i image store från dess rot. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-store-root-info"></a>sfctl store rot-info
Hämtar innehållsinformationen i roten för image store.

Returnerar information om image store innehåll i roten för image store.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-store-stat"></a>sfctl store stat
Hämtar innehållsinformation för image store.

Returnerar information om image store innehållet på den angivna contentPath. ContentPath är i förhållande till roten för image store.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --innehåll sökväg [krävs] | Relativ sökväg till filen eller mappen i image store från dess rot. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).