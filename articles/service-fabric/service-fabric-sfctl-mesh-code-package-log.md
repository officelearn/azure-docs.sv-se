---
title: Azure Service Fabric CLI – sfctl-nätkod – paket-logg | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl nät Code-Package-log-kommandon.
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
ms.openlocfilehash: b1949f87dcdb1e3d9fe8e7fd08d8d8ba3b8203a0
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036441"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Hämta loggarna för behållaren för det angivna kod paketet för den angivna tjänst repliken.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Hämta | Hämtar loggarna från behållaren. |

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
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).