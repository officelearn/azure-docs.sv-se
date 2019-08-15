---
title: Azure Service Fabric CLI – sfctl, mask-Gateway | Microsoft Docs
description: Beskriver kommandona för masken för Service Fabric CLI-sfctl.
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
ms.openlocfilehash: b8acb23b274e6062ae3c50ab80220ab93297037e
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036429"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Hämta och ta bort nät gateway-resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| radera | Tar bort Gateway-resursen. |
| list | Visar en lista över alla gateway-resurser. |
| visa | Hämtar Gateway-resursen med det aktuella namnet. |

## <a name="sfctl-mesh-gateway-delete"></a>ta bort sfctls mask Gateway
Tar bort Gateway-resursen.

Tar bort Gateway-resursen som identifieras med namnet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Namnet på Gateway-resursen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl nät Gateway-lista
Visar en lista över alla gateway-resurser.

Hämtar information om alla gateway-resurser i en specifik resurs grupp. Informationen innehåller en beskrivning av och andra egenskaper för gatewayen.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl nät Gateway show
Hämtar Gateway-resursen med det aktuella namnet.

Hämtar information om Gateway-resursen med det aktuella namnet. Informationen innehåller en beskrivning av och andra egenskaper för gatewayen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Namnet på Gateway-resursen. |

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