---
title: Azure Service Fabric CLI – sfctl Store | Microsoft Docs
description: Beskriver Service Fabric CLI-kommandon för sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: ccd9e8323f6e0de7b81c7600e7828e4858c51201
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035858"
---
# <a name="sfctl-store"></a>sfctl store
Utför grundläggande åtgärder på filnivå på klustrets avbildnings lager.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| radera | Tar bort befintligt bild lagrings innehåll. |
| rot-info | Hämtar innehålls informationen i roten för avbildnings arkivet. |
| indikerar | Hämtar innehålls informationen för avbildnings arkivet. |

## <a name="sfctl-store-delete"></a>ta bort sfctl Store
Tar bort befintligt bild lagrings innehåll.

Tar bort befintligt avbildnings lagrings innehåll som hittas inom den angivna relativa sökvägen för avbildnings arkivet. Det här kommandot kan användas för att ta bort uppladdade programpaket när de har tillhandahållits.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --innehålls Sök väg [obligatoriskt] | Relativ sökväg till fil eller mapp i avbildnings arkivet från roten. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-store-root-info"></a>sfctl Store-rot – info
Hämtar innehålls informationen i roten för avbildnings arkivet.

Returnerar informationen om avbildningens lagrings innehåll i roten på avbildnings lagrings platsen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-store-stat"></a>sfctl Store stat
Hämtar innehålls informationen för avbildnings arkivet.

Returnerar informationen om avbildningens lagrings innehåll på angiven contentPath. ContentPath är i förhållande till roten för avbildnings arkivet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --innehålls Sök väg [obligatoriskt] | Relativ sökväg till fil eller mapp i avbildnings arkivet från roten. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).