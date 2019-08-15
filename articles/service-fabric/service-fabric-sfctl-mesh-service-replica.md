---
title: Azure Service Fabric CLI-sfctl nät tjänst – replik | Microsoft Docs
description: Beskriver kommandot Service Fabric CLI sfctl nät service-Replica.
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
ms.openlocfilehash: 6819bb32eecf8477e2c0727b50641858db21c784
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035915"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Hämta replik information och lista repliker för en specifik tjänst i en program resurs.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| list | Visar en lista över alla repliker av en tjänst. |
| visa | Hämtar den aktuella repliken av tjänsten för ett program. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl nät tjänst – replik lista
Visar en lista över alla repliker av en tjänst.

Hämtar information om alla repliker av en tjänst. Informationen inkluderar beskrivning och andra egenskaper för tjänst repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name--program-Name [required] | Namnet på programmet. |
| --tjänst namn [obligatoriskt] | Namnet på tjänsten. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl nät tjänst – replik show
Hämtar den aktuella repliken av tjänsten för ett program.

Hämtar information om tjänst repliken med det aktuella namnet. Informationen inkluderar beskrivning och andra egenskaper för tjänst repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name--program-Name [required] | Namnet på programmet. |
| --Name-n [required] | Namnet på tjänst repliken. |
| --tjänst namn [obligatoriskt] | Namnet på tjänsten. |

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