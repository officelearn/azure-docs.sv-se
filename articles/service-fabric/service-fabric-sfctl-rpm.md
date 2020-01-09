---
title: Azure Service Fabric CLI – sfctl rpm
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för tjänsten Repair Manager.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 674970276046034d13801db7c1bb4ab5175385fb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639096"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Fråga och skicka kommandon till tjänsten Repair Manager.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Godkänn-Force | Tvingar godkännandet av den aktuella reparations uppgiften. |
| delete | Tar bort en slutförd reparations uppgift. |
| lista | Hämtar en lista över reparations aktiviteter som matchar de aktuella filtren. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm-godkännande-Force
Tvingar godkännandet av den aktuella reparations uppgiften.

Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --aktivitets-ID [obligatoriskt] | ID för reparations uppgiften. |
| --version | Reparations uppgiftens aktuella versions nummer. Om den inte är noll kommer begäran endast att lyckas om det här värdet matchar den faktiska aktuella versionen av reparations aktiviteten. Om värdet är noll utförs ingen versions kontroll. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-rpm-delete"></a>ta bort sfctl rpm
Tar bort en slutförd reparations uppgift.

Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --aktivitets-ID [obligatoriskt] | ID för den slutförda reparations uppgiften som ska tas bort. |
| --version | Reparations uppgiftens aktuella versions nummer. Om den inte är noll kommer begäran endast att lyckas om det här värdet matchar den faktiska aktuella versionen av reparations aktiviteten. Om värdet är noll utförs ingen versions kontroll. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-rpm-list"></a>sfctl rpm-lista
Hämtar en lista över reparations aktiviteter som matchar de aktuella filtren.

Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --executor-filter | Namnet på den reparations-utförar vars begärda uppgifter ska tas med i listan. |
| --state-filter | Ett bitvis-eller av följande värden som anger vilka uppgifts tillstånd som ska ingå i resultat listan. <br> 1 – skapad <br>2-begärd  <br>4 – förbereder  <br>8-godkänd  <br>16-körning  <br>32 – återställning  <br>64-slutförd |
| --task-id-filter | Det ID-prefix för reparations aktivitet som ska matchas. |

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