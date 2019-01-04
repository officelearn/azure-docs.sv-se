---
title: Azure Service Fabric CLI - sfctl nät secretvalue | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl nät secretvalue kommandon.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 064aeaea47dd59a1dd75cf19ea4060d8f9c2c4bf
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53559067"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Hämta och ta bort nät secretvalue resurser.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| delete | Tar bort det angivna värdet för den namngivna hemliga resursen. |
| lista | Lista över namnen på alla värden i den angivna hemliga resursen. |
| visa | Hämta värdet för en angiven version av en hemlig resurs. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl nät secretvalue delete
Tar bort det angivna värdet för den namngivna hemliga resursen.

Tar bort hemligt värde-resurs som identifieras av namnet. Namnet på resursen är vanligtvis den version som är associerade med detta värde. Borttagning av misslyckas om det angivna värdet används.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --hemlighet-name - n [krävs] | Namnet på den hemliga resursen. |
| --version - v [krävs] | Namnet på den hemliga versionen. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl nät secretvalue lista
Lista över namnen på alla värden i den angivna hemliga resursen.

Hämtar information om alla resurser som hemligt värde för den angivna hemliga resursen. Informationen omfattar namnen på resurserna som hemligt värde, men inte de faktiska värdena.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --hemlighet-name - n [krävs] | Namnet på den hemliga resursen. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl nät secretvalue show
Hämta värdet för en angiven version av en hemlig resurs.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --hemlighet-name - n [krävs] | Namnet på den hemliga resursen. |
| --version - v [krävs] | Namnet på den hemliga versionen. |
| – Visa värde | Visa det faktiska värdet för den hemliga versionen. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).