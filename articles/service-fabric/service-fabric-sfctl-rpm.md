---
title: Azure Service Fabric CLI- sfctl rpm
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för reparationshanterarens tjänst.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904939"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Fråga och skicka kommandon till reparationshanteraren.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| godkänna-kraft | Tvingar godkännandet av den angivna reparationsuppgiften. |
| delete | Tar bort en slutförd reparationsaktivitet. |
| lista | Hämtar en lista över reparationsuppgifter som matchar de angivna filtren. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm godkänna-kraft
Tvingar godkännandet av den angivna reparationsuppgiften.

Det här API:et stöder plattformen Service Fabric. Det är inte tänkt att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --task-id [Obligatoriskt] | ID:et för reparationsaktiviteten. |
| --version | Det aktuella versionsnumret för reparationsaktiviteten. Om begäran inte är noll lyckas begäran bara om det här värdet matchar den aktuella versionen av reparationsaktiviteten. Om noll utförs ingen versionskontroll. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm ta bort
Tar bort en slutförd reparationsaktivitet.

Det här API:et stöder plattformen Service Fabric. Det är inte tänkt att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --task-id [Obligatoriskt] | ID:et för den slutförda reparationsuppgift som ska tas bort. |
| --version | Det aktuella versionsnumret för reparationsaktiviteten. Om begäran inte är noll lyckas begäran bara om det här värdet matchar den aktuella versionen av reparationsaktiviteten. Om noll utförs ingen versionskontroll. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-rpm-list"></a>sfctl rpm lista
Hämtar en lista över reparationsuppgifter som matchar de angivna filtren.

Det här API:et stöder plattformen Service Fabric. Det är inte tänkt att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --executor-filter | Namnet på den reparationsutserledare vars påstådda uppgifter ska tas med i listan. |
| --state-filter | Ett bitvis-ELLER av följande värden, som anger vilka aktivitetstillstånd som ska ingå i resultatlistan. <ul><li>1 - Skapad</li><li>2 - Hävdade</li><li>4 - Förberedelser</li><li>8 - Godkänd</li><li>16 - Utförande</li><li>32 - Återställa</li><li>64 - Avslutad</li></ul>
| --task-id-filter | Prefixet för reparationsuppgifts-ID som ska matchas. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).
