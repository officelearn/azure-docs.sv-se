---
title: Azure Service Fabric CLI – sfctl-nätkod – paket-logg | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl nät Code-Package-log-kommandon.
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
ms.openlocfilehash: d1f0e34389a48b79c049f26e8b04c870f0f1a9a7
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901270"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Hämta loggarna för behållaren för det angivna kod paketet för den angivna tjänst repliken.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| ta | Hämtar loggarna från behållaren. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl-nätkod-paket-logg hämtning
Hämtar loggarna från behållaren.

Hämtar loggarna för behållaren för det angivna kod paketet för tjänst repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name--program-Name [required] | Namnet på programmet. |
| --kod-paket namn [obligatoriskt] | Namn på tjänstens kod paket. |
| --replik-Name [required] | Service Fabric replikens namn. |
| --tjänst namn [obligatoriskt] | Namnet på tjänsten. |
| --pilslut | Antal rader som ska visas från slutet av loggarna. Standardvärdet är 100. all för att visa fullständiga loggar. |

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