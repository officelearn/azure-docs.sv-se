---
title: Azure Service Fabric CLI - sfctl inställningar telemetri | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl inställningar telemetri kommandon.
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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: e0e229f0edb078fe9ce289d0089f34d7cbf9dbf8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285398"
---
# <a name="sfctl-settings-telemetry"></a>sfctl inställningar telemetri
Konfigurera telemetriinställningar för som är lokala för den här instansen av sfctl.

Sfctl telemetri samlar in kommandonamnet utan parametrar som tillhandahålls eller deras värden sfctl version, OS-typ, python-version, lyckas eller misslyckas kommandot returnerade ett felmeddelande.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| set-telemetri | Aktivera eller inaktivera telemetri. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl inställningar telemetri set-telemetri
Aktivera eller inaktivera telemetri.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --av | Inaktivera telemetri. |
| --på | Aktivera telemetri. Detta är standardvärdet. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

### <a name="examples"></a>Exempel

Inaktivera telemetri.

```
sfctl settings telemetry set_telemetry --off
```

Aktivera telemetri.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).