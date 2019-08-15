---
title: Azure Service Fabric CLI – sfctl-nätappen | Microsoft Docs
description: Beskriver programkommandona för Service Fabric kommandot CLI sfctl-nätappar.
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
ms.openlocfilehash: 7e560b08290146b4a497539ecc180f8ae4431246
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035160"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
Hämta och ta bort program resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| radera | Tar bort program resursen. |
| list | Visar en lista över alla program resurser. |
| visa | Hämtar program resursen med det aktuella namnet. |

## <a name="sfctl-mesh-app-delete"></a>sfctl nätappen ta bort
Tar bort program resursen.

Tar bort program resursen som identifieras med namnet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Namnet på programmet. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-app-list"></a>sfctl-nätlista
Visar en lista över alla program resurser.

Hämtar information om alla program resurser i en specifik resurs grupp. Informationen inkluderar beskrivningen och andra egenskaper för programmet.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-app-show"></a>sfctl nät app show
Hämtar program resursen med det aktuella namnet.

Hämtar information om program resursen med det aktuella namnet. Informationen inkluderar beskrivningen och andra egenskaper för programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Name-n [required] | Namnet på programmet. |

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