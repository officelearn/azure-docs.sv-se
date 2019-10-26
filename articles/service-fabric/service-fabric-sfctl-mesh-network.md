---
title: Azure Service Fabric CLI – sfctl nät nätverk | Microsoft Docs
description: Beskriver nätverks kommandona för det Service Fabric CLI-sfctl nät.
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
ms.openlocfilehash: 4d74e3d37891ad50d8bbaa69757d0d99c80591cc
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901222"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Hämta och ta bort nätverks resurser för nätverk.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort nätverks resursen. |
| lista | Visar en lista över alla nätverks resurser. |
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
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-network-list"></a>sfctl nät nätverks lista
Visar en lista över alla nätverks resurser.

Hämtar information om alla nätverks resurser i en specifik resurs grupp. Informationen inkluderar beskrivningen och andra egenskaper för nätverket.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
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
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).