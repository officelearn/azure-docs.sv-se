---
title: Azure Service Fabric CLI – sfctl nät secretvalue | Microsoft Docs
description: Beskriver startsecretvalue-kommandona för Service Fabric CLI-sfctl.
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
ms.openlocfilehash: 5200c40afe62ce3a236a3a16b59b64deb8d24ced
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035937"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Hämta och ta bort nät secretvalue-resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| radera | Tar bort det angivna värdet för den namngivna hemliga resursen. |
| list | Lista namn på alla värden för den angivna hemliga resursen. |
| visa | Hämta värdet för en angiven version av en hemlig resurs. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl nät secretvalue ta bort
Tar bort det angivna värdet för den namngivna hemliga resursen.

Tar bort den hemliga värde resurs som identifieras med namnet. Namnet på resursen är vanligt vis den version som är kopplad till det värdet. Det går inte att ta bort om det angivna värdet används.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Secret-Name-n [required] | Namnet på den hemliga resursen. |
| --version-v [obligatoriskt] | Namnet på den hemliga versionen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl nät secretvalue-lista
Lista namn på alla värden för den angivna hemliga resursen.

Hämtar information om alla hemliga värde resurser för den angivna hemliga resursen. Informationen innehåller namnen på de hemliga värde resurserna, men inte de faktiska värdena.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Secret-Name-n [required] | Namnet på den hemliga resursen. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl nät secretvalue show
Hämta värdet för en angiven version av en hemlig resurs.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Secret-Name-n [required] | Namnet på den hemliga resursen. |
| --version-v [obligatoriskt] | Namnet på den hemliga versionen. |
| --show-Value | Visa det faktiska värdet för den hemliga versionen. |

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