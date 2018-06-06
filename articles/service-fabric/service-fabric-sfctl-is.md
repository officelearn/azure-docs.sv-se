---
title: Azure Service Fabric CLI - sfctl är | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl är kommandon.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: aa1e4d588f367351f0bed10370c7d67d50dd9927
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763059"
---
# <a name="sfctl-is"></a>sfctl is
Fråga efter och skicka kommandon till tjänsten infrastruktur.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| kommandot | Initierar en administrativa kommando på den angivna Infrastructure Service-instansen. |
| DocumentDB | Anropar en skrivskyddad fråga på tjänstinstansen angivna infrastruktur. |

## <a name="sfctl-is-command"></a>sfctl är kommando
Initierar en administrativa kommando på den angivna Infrastructure Service-instansen.

För kluster som har en eller flera instanser av tjänsten infrastruktur som konfigurerats för kan detta API skicka infrastruktur-fil till en viss instans av tjänsten infrastruktur. Tillgängliga kommandon och deras motsvarande svar format varierar beroende på den infrastruktur som körs på klustret. Detta API stöder Service Fabric-plattform. Det är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --kommando [krävs] | Texten för kommandot som ska startas. Innehållet i kommandot är infrastruktur-specifika. |
| --tjänst-id | Identiteten för infrastruktur-tjänst. <br><br> Detta är det fullständiga namnet på tjänsten infrastruktur utan 'fabric-URI-schemat. Den här parametern krävs endast för det kluster som har mer än en instans av infrastruktur-tjänsten körs. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-is-query"></a>sfctl är fråga
Anropar en skrivskyddad fråga på tjänstinstansen angivna infrastruktur.

För kluster som har en eller flera instanser av tjänsten-infrastruktur konfigurerad, är detta API ett sätt att skicka infrastruktur-specifika frågor till en viss instans av tjänsten infrastruktur. Tillgängliga kommandon och deras motsvarande svar format varierar beroende på den infrastruktur som körs på klustret. Detta API stöder Service Fabric-plattform. Det är inte avsedd att användas direkt från din kod.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --kommando [krävs] | Texten för kommandot som ska startas. Innehållet i kommandot är infrastruktur-specifika. |
| --tjänst-id | Identiteten för infrastruktur-tjänst. <br><br> Detta är det fullständiga namnet på tjänsten infrastruktur utan den ' fabric\:-URI-schema. Den här parametern krävs endast för det kluster som har mer än en instans av infrastruktur-tjänsten körs. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).