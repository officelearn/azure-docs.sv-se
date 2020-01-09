---
title: Azure Service Fabric CLI – sfctl är
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista med kommandon för att hantera infrastrukturen.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: c3c98e8da0c2a5449296a0fd108977ec0633f83d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639130"
---
# <a name="sfctl-is"></a>sfctl is
Fråga och skicka kommandon till infrastruktur tjänsten.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| command | Anropar ett administrativt kommando på den aktuella infrastruktur tjänst instansen. |
| DocumentDB | Anropar en skrivskyddad fråga på den aktuella infrastruktur tjänst instansen. |

## <a name="sfctl-is-command"></a>sfctl är kommando
Anropar ett administrativt kommando på den aktuella infrastruktur tjänst instansen.

För kluster som har en eller flera instanser av infrastruktur tjänsten kan du använda det här API: et för att skicka Infrastructure-specifika kommandon till en viss instans av infrastruktur tjänsten. Tillgängliga kommandon och deras motsvarande svars format varierar beroende på den infrastruktur som klustret körs på. Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kommando [required] | Texten för kommandot som ska anropas. Kommandots innehåll är Infrastructure-Specific. |
| --service-ID | Infrastruktur tjänstens identitet. <br><br> Detta är det fullständiga namnet på infrastruktur tjänsten utan URI-schemat "Fabric\:". Den här parametern krävs bara för kluster som har fler än en instans av infrastruktur tjänsten som kör. |
| --timeout-t | Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-is-query"></a>sfctl är fråga
Anropar en skrivskyddad fråga på den aktuella infrastruktur tjänst instansen.

För kluster som har en eller flera instanser av infrastruktur tjänsten är det här API: et som är ett sätt att skicka infrastruktur specifika frågor till en viss instans av infrastruktur tjänsten. Tillgängliga kommandon och deras motsvarande svars format varierar beroende på den infrastruktur som klustret körs på. Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kommando [required] | Texten för kommandot som ska anropas. Kommandots innehåll är Infrastructure-Specific. |
| --service-ID | Infrastruktur tjänstens identitet. <br><br> Detta är det fullständiga namnet på infrastruktur tjänsten utan URI-schemat "Fabric\:". Den här parametern krävs bara för kluster som har fler än en instans av infrastruktur tjänsten som kör. |
| --timeout-t | Standard\: 60. |

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