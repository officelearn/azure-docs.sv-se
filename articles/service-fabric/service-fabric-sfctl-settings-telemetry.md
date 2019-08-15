---
title: Azure Service Fabric CLI – sfctl inställnings telemetri | Microsoft Docs
description: Beskriver telemetri-kommandona för Service Fabric CLI-sfctl inställningar.
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
ms.openlocfilehash: cf5ebbeb4d9b4757e0c55eeb1a9268065efb2c7c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035197"
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
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
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
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).