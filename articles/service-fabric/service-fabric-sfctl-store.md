---
title: Azure Service Fabric CLI – sfctl Store | Microsoft Docs
description: Beskriver Service Fabric CLI-kommandon för sfctl.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: e8a085c4aa6df34441f22da5542231999930d89f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900946"
---
# <a name="sfctl-store"></a>sfctl store
Utför grundläggande åtgärder på filnivå på klustrets avbildnings lager.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort befintligt bild lagrings innehåll. |
| rot-info | Hämtar innehålls informationen i roten för avbildnings arkivet. |
| indikerar | Hämtar innehålls informationen för avbildnings arkivet. |

## <a name="sfctl-store-delete"></a>ta bort sfctl Store
Tar bort befintligt bild lagrings innehåll.

Tar bort befintligt avbildnings lagrings innehåll som hittas inom den angivna relativa sökvägen för avbildnings arkivet. Det här kommandot kan användas för att ta bort uppladdade programpaket när de har tillhandahållits.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --innehålls Sök väg [obligatoriskt] | Relativ sökväg till fil eller mapp i avbildnings arkivet från roten. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-store-root-info"></a>sfctl Store-rot – info
Hämtar innehålls informationen i roten för avbildnings arkivet.

Returnerar informationen om avbildningens lagrings innehåll i roten på avbildnings lagrings platsen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-store-stat"></a>sfctl Store stat
Hämtar innehålls informationen för avbildnings arkivet.

Returnerar informationen om avbildningens lagrings innehåll på angiven contentPath. ContentPath är i förhållande till roten för avbildnings arkivet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --innehålls Sök väg [obligatoriskt] | Relativ sökväg till fil eller mapp i avbildnings arkivet från roten. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).