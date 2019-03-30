---
title: Azure Service Fabric CLI - sfctl rpm | Microsoft Docs
description: Beskriver sfctl rpm-kommandon för Service Fabric CLI.
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
ms.openlocfilehash: 04080d75042bfa8a07533336936165e0abef051b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664140"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Fråga efter och skicka kommandon till reparera manager-tjänsten.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Godkänn-force | Tvingar godkännande av angivna reparationsuppgiften. |
| delete | Tar bort en slutförd reparationsuppgiften. |
| lista | Hämtar en lista över reparera uppgifter som matchar de angivna filtren. |

## <a name="sfctl-rpm-approve-force"></a>sfctl godkänna rpm-force
Tvingar godkännande av angivna reparationsuppgiften.

Detta API stöder Service Fabric-plattform. Det är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --uppgift-id [krävs] | ID för reparationsuppgiften. |
| --version | Det aktuella versionsnumret för reparationsuppgiften. Om inte är noll, sedan lyckas begäran endast om det här värdet matchar den faktiska aktuella versionen av reparationsuppgiften. Om noll, utförs ingen kontroll av version. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
Tar bort en slutförd reparationsuppgiften.

Detta API stöder Service Fabric-plattform. Det är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --uppgift-id [krävs] | ID för slutförda reparationsuppgiften som ska tas bort. |
| --version | Det aktuella versionsnumret för reparationsuppgiften. Om inte är noll, sedan lyckas begäran endast om det här värdet matchar den faktiska aktuella versionen av reparationsuppgiften. Om noll, utförs ingen kontroll av version. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-rpm-list"></a>sfctl rpm-lista
Hämtar en lista över reparera uppgifter som matchar de angivna filtren.

Detta API stöder Service Fabric-plattform. Det är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --executor-filter | Namnet på repair-executor vars anspråk uppgifter ska tas med i listan. |
| --state-filter | Ett logiskt eller av följande värden som anger vilken uppgift tillstånd ska inkluderas i resultatlistan. <br> 1 – skapa <br>2 – ägs  <br>4 – Förbereda  <br>8 - godkänd  <br>16 - körning  <br>32 - återställning  <br>64 - har slutförts |
| --task-id-filter | Reparera uppgift ID-prefixet som ska matchas. |

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