---
title: Azure Service Fabric CLI - sfctl inställningar telemetri | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl inställningar telemetri kommandon.
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
ms.openlocfilehash: 42a82ab0be37f260a48a1da6cecab5120c24d293
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669273"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
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

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
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