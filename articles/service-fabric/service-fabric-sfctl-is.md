---
title: Azure Service Fabric CLI-sfctl är | Microsoft Docs
description: Beskriver kommandona för Service Fabric CLI-sfctl.
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
ms.openlocfilehash: 998a94350250402d4face64f64e12f32cf5b2a36
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036486"
---
# <a name="sfctl-is"></a>sfctl is
Fråga och skicka kommandon till infrastruktur tjänsten.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Kommandot | Anropar ett administrativt kommando på den aktuella infrastruktur tjänst instansen. |
| query | Anropar en skrivskyddad fråga på den aktuella infrastruktur tjänst instansen. |

## <a name="sfctl-is-command"></a>sfctl är kommando
Anropar ett administrativt kommando på den aktuella infrastruktur tjänst instansen.

För kluster som har en eller flera instanser av infrastruktur tjänsten kan du använda det här API: et för att skicka Infrastructure-specifika kommandon till en viss instans av infrastruktur tjänsten. Tillgängliga kommandon och deras motsvarande svars format varierar beroende på den infrastruktur som klustret körs på. Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kommando [required] | Texten för kommandot som ska anropas. Kommandots innehåll är Infrastructure-Specific. |
| --service-ID | Infrastruktur tjänstens identitet. <br><br> Detta är det fullständiga namnet på infrastruktur tjänsten utan URI-schemat "\:Fabric". Den här parametern krävs bara för kluster som har fler än en instans av infrastruktur tjänsten som kör. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-is-query"></a>sfctl är fråga
Anropar en skrivskyddad fråga på den aktuella infrastruktur tjänst instansen.

För kluster som har en eller flera instanser av infrastruktur tjänsten är det här API: et som är ett sätt att skicka infrastruktur specifika frågor till en viss instans av infrastruktur tjänsten. Tillgängliga kommandon och deras motsvarande svars format varierar beroende på den infrastruktur som klustret körs på. Detta API stöder Service Fabric plattform; den är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kommando [required] | Texten för kommandot som ska anropas. Kommandots innehåll är Infrastructure-Specific. |
| --service-ID | Infrastruktur tjänstens identitet. <br><br> Detta är det fullständiga namnet på infrastruktur tjänsten utan URI-schemat "\:Fabric". Den här parametern krävs bara för kluster som har fler än en instans av infrastruktur tjänsten som kör. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

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