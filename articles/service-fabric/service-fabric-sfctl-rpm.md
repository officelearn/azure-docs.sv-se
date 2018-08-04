---
title: Azure Service Fabric CLI - sfctl rpm | Microsoft Docs
description: Beskriver sfctl rpm-kommandon för Service Fabric CLI.
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
ms.openlocfilehash: 57a9f0516175b459723a3dcdb2e3766f0fa039c1
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495432"
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

|Argumentet|Beskrivning|
| --- | --- |
| --uppgift-id [krävs] | ID för reparationsuppgiften. |
| --version | Det aktuella versionsnumret för reparationsuppgiften. Om inte är noll, sedan lyckas begäran endast om det här värdet matchar den faktiska aktuella versionen av reparationsuppgiften. Om noll, utförs ingen kontroll av version. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
Tar bort en slutförd reparationsuppgiften.

Detta API stöder Service Fabric-plattform. Det är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --uppgift-id [krävs] | ID för slutförda reparationsuppgiften som ska tas bort. |
| --version | Det aktuella versionsnumret för reparationsuppgiften. Om inte är noll, sedan lyckas begäran endast om det här värdet matchar den faktiska aktuella versionen av reparationsuppgiften. Om noll, utförs ingen kontroll av version. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-rpm-list"></a>sfctl rpm-lista
Hämtar en lista över reparera uppgifter som matchar de angivna filtren.

Detta API stöder Service Fabric-plattform. Det är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --executor-filter | Namnet på repair-executor vars anspråk uppgifter ska tas med i listan. |
| --tillstånd-filter | Ett logiskt eller av följande värden som anger vilken uppgift tillstånd ska inkluderas i resultatlistan. <br> 1 – skapa <br>2 – ägs  <br>4 – Förbereda  <br>8 - godkänd  <br>16 - körning  <br>32 - återställning  <br>64 - har slutförts |
| --task-id-filter | Reparera uppgift ID-prefixet som ska matchas. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).