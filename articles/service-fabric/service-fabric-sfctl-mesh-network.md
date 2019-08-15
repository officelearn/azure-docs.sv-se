---
title: Azure Service Fabric CLI – sfctl nät nätverk | Microsoft Docs
description: Beskriver nätverks kommandona för det Service Fabric CLI-sfctl nät.
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
ms.openlocfilehash: e92fbc3856bba69db11aba6ad5d85e814bf30a81
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035048"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Hämta och ta bort nätverks resurser för nätverk.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| radera | Tar bort nätverks resursen. |
| list | Visar en lista över alla nätverks resurser. |
| visa | Hämtar nätverks resursen med det aktuella namnet. |

## <a name="sfctl-mesh-network-delete"></a>sfctl nät nätverks borttagning
Tar bort nätverks resursen.

Tar bort nätverks resursen som identifieras med namnet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Nätverkets namn. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-network-list"></a>sfctl nät nätverks lista
Visar en lista över alla nätverks resurser.

Hämtar information om alla nätverks resurser i en specifik resurs grupp. Informationen inkluderar beskrivningen och andra egenskaper för nätverket.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-network-show"></a>sfctl nät nätverk show
Hämtar nätverks resursen med det aktuella namnet.

Hämtar information om nätverks resursen med det aktuella namnet. Informationen inkluderar beskrivningen och andra egenskaper för nätverket.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Nätverkets namn. |

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