---
title: Azure Service Fabric CLI – sfctl inställningar telemetri
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att konfigurera sfctl telemetri.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ef720a14617b4131474d50875701d0ef27df4151
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245523"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Konfigurera inställningar för telemetri lokalt till den här instansen av sfctl.

Sfctl-telemetri samlar in kommando namn utan parametrar som tillhandahålls eller deras värden, Sfctl-version, OS-typ, python-version, lyckad eller misslyckad av kommandot, fel meddelandet som returneras.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Ange telemetri | Aktivera eller Inaktivera telemetri. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl inställningar telemetri set-telemetri
Aktivera eller Inaktivera telemetri.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| -av | Stäng av telemetri. |
| --på | Aktivera telemetri. Detta är standardvärdet. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden \: JSON, jsonc, Table, TSV.  Standard- \: JSON. |
| --fråga | Frågesträngen JMESPath. Se http \: //jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

### <a name="examples"></a>Exempel

Stäng av telemetri.

```
sfctl settings telemetry set_telemetry --off
```

Aktivera telemetri.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](./scripts/sfctl-upgrade-application.md).
