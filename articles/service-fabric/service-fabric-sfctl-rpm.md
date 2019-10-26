---
title: Azure Service Fabric CLI – sfctl rpm | Microsoft Docs
description: Beskriver kommandona för kommandot Service Fabric CLI sfctl RPM.
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
ms.openlocfilehash: 9c37dc8131f14a3b35e68b3e88502c91f96810f6
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901035"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Fråga och skicka kommandon till tjänsten Repair Manager.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Godkänn-Force | Tvingar godkännandet av den aktuella reparations uppgiften. |
| delete | Tar bort en slutförd reparations uppgift. |
| lista | Hämtar en lista över reparations aktiviteter som matchar de aktuella filtren. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm-godkännande-Force
Tvingar godkännandet av den aktuella reparations uppgiften.

Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --aktivitets-ID [obligatoriskt] | ID för reparations uppgiften. |
| --version | Reparations uppgiftens aktuella versions nummer. Om den inte är noll kommer begäran endast att lyckas om det här värdet matchar den faktiska aktuella versionen av reparations aktiviteten. Om värdet är noll utförs ingen versions kontroll. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-rpm-delete"></a>ta bort sfctl rpm
Tar bort en slutförd reparations uppgift.

Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --aktivitets-ID [obligatoriskt] | ID för den slutförda reparations uppgiften som ska tas bort. |
| --version | Reparations uppgiftens aktuella versions nummer. Om den inte är noll kommer begäran endast att lyckas om det här värdet matchar den faktiska aktuella versionen av reparations aktiviteten. Om värdet är noll utförs ingen versions kontroll. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-rpm-list"></a>sfctl rpm-lista
Hämtar en lista över reparations aktiviteter som matchar de aktuella filtren.

Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --utförar-filter | Namnet på den reparations-utförar vars begärda uppgifter ska tas med i listan. |
| --tillstånd-filter | Ett bitvis-eller av följande värden som anger vilka uppgifts tillstånd som ska ingå i resultat listan. <br> 1 – skapad <br>2-begärd  <br>4 – förbereder  <br>8-godkänd  <br>16-körning  <br>32 – återställning  <br>64-slutförd |
| --aktivitets-ID-filter | Det ID-prefix för reparations aktivitet som ska matchas. |

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