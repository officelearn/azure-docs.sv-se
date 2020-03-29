---
title: Azure Service Fabric CLI- sfctl-inställningar telemetri
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för att konfigurera sfctl telemetri.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903027"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Konfigurera telemetriinställningar lokalt för den här instansen av sfctl.

Sfctl telemetri samlar in kommandonamn utan parametrar som tillhandahålls eller deras värden, sfctl version, OS-typ, python version, framgång eller fel på kommandot, felmeddelandet returneras.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| set-telemetri | Slå på eller stänga av telemetri. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl-inställningar telemetri set-telemetri
Slå på eller stänga av telemetri.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| - Av | Stäng av telemetri. |
| - på | Slå på telemetri. Detta är standardvärdet. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

### <a name="examples"></a>Exempel

Stäng av telemetri.

```
sfctl settings telemetry set_telemetry --off
```

Slå på telemetri.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).