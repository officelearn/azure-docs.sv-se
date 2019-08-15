---
title: Azure Service Fabric CLI – sfctl, nät volym | Microsoft Docs
description: Beskriver sfctl CLI-volym kommandon Service Fabric för masken.
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
ms.openlocfilehash: 4e2413a152048e08d6218783891c20b66ac2e618
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034990"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Hämta och ta bort volym resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| radera | Tar bort volym resursen. |
| list | Visar en lista över alla volym resurser. |
| visa | Hämtar volym resursen med det aktuella namnet. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl nät volym borttagning
Tar bort volym resursen.

Tar bort volym resursen som identifieras av namnet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Namnet på volymen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-volume-list"></a>sfctl nät volym lista
Visar en lista över alla volym resurser.

Hämtar information om alla volym resurser i en specifik resurs grupp. Informationen inkluderar beskrivning och andra egenskaper för volymen.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-volume-show"></a>sfctl nät volym Visa
Hämtar volym resursen med det aktuella namnet.

Hämtar information om volym resursen med det aktuella namnet. Informationen inkluderar beskrivning och andra egenskaper för volymen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Namnet på volymen. |

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