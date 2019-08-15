---
title: Azure Service Fabric CLI – sfctl nät tjänst | Microsoft Docs
description: Beskriver kommandona för mask tjänst för Service Fabric CLI-sfctl.
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
ms.openlocfilehash: 6afcb891de763f156705bc9825ab7575a272c1db
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035037"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Hämta tjänst information och lista över tjänster för en program resurs.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| list | Visar en lista över alla tjänst resurser. |
| visa | Hämtar tjänst resursen med det aktuella namnet. |

## <a name="sfctl-mesh-service-list"></a>sfctl nät tjänst lista
Visar en lista över alla tjänst resurser.

Hämtar information om alla tjänster i en program resurs. Informationen inkluderar beskrivning och andra egenskaper för tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name--program-Name [required] | Namnet på programmet. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-service-show"></a>sfctl nät tjänst show
Hämtar tjänst resursen med det aktuella namnet.

Hämtar information om tjänst resursen med det aktuella namnet. Informationen inkluderar beskrivning och andra egenskaper för tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --App-Name--program-Name [required] | Namnet på programmet. |
| --Name-n [required] | Namnet på tjänsten. |

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