---
title: Azure Service Fabric CLI - sfctl rpm | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl rpm-kommandon.
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
ms.openlocfilehash: 5e7cf1b28e8f38f0539e0084e1f745e0dab4dec3
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763620"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Fråga efter och skicka kommandon till reparera manager-tjänsten.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Godkänn kraft | Tvingar godkännande av angivna reparationsuppgiften. |
| radera | Tar bort en slutförd reparationsuppgiften. |
| lista | Hämtar en lista över reparera aktiviteter som matchar de angivna filtren. |

## <a name="sfctl-rpm-approve-force"></a>Godkänn sfctl rpm-force
Tvingar godkännande av angivna reparationsuppgiften.

Detta API stöder Service Fabric-plattform. Det är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --aktivitet-id [krävs] | ID för reparationsuppgiften. |
| --version | Det aktuella versionsnumret för reparationsuppgiften. Om inte är noll, sedan lyckas begäran endast om det här värdet matchar den faktiska aktuella versionen av reparationsuppgiften. Om noll, utförs ingen kontroll av version. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-rpm-delete"></a>ta bort sfctl rpm
Tar bort en slutförd reparationsuppgiften.

Detta API stöder Service Fabric-plattform. Det är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --aktivitet-id [krävs] | ID för slutförda reparationsuppgiften som ska tas bort. |
| --version | Det aktuella versionsnumret för reparationsuppgiften. Om inte är noll, sedan lyckas begäran endast om det här värdet matchar den faktiska aktuella versionen av reparationsuppgiften. Om noll, utförs ingen kontroll av version. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-rpm-list"></a>sfctl rpm-lista
Hämtar en lista över reparera aktiviteter som matchar de angivna filtren.

Detta API stöder Service Fabric-plattform. Det är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --executor-filter | Namnet på den reparera utföraren vars påstått aktiviteter som ska tas med i listan. |
| --tillstånd-filter | Ett logiskt eller av följande värden som anger vilken uppgift tillstånd ska inkluderas i resultatlistan. <br> 1 - skapas <br>2 - anspråk  <br>4 - förbereder  <br>8 - godkänd  <br>16 - körning  <br>32 - återställning  <br>64 - slutförd |
| --task-id-filter | Det reparera uppgift ID-prefixet som ska matchas. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).