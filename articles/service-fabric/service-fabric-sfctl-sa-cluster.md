---
title: Azure Service Fabric CLI - sfctl sa-kluster | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl fristående kluster-kommandon.
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
ms.openlocfilehash: ffdbff7edc5af187071615c8b1e61790b3a38429
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764040"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-kluster
Hantera fristående Service Fabric-kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Config | Hämta Service Fabric fristående klusterkonfigurationen. |
| config-uppgradering | Börja uppgradera konfigurationen av en fristående Service Fabric-klustret. |
| uppgradera status | Konfigurationsstatusen klustret uppgradering av en fristående Service Fabric-klustret. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-klusterkonfigurationen
Hämta Service Fabric fristående klusterkonfigurationen.

Hämta Service Fabric fristående klusterkonfigurationen. Klusterkonfigurationen innehåller egenskaper för klustret med olika nodtyper på klustret, säkerhetskonfigurationer, fel, och uppgraderingsdomänen topologier, osv.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --konfiguration-api-versionen [krävs] | API-versionen av fristående json klusterkonfigurationen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-kluster config-uppgradering
Börja uppgradera konfigurationen av en fristående Service Fabric-klustret.

Validera de angivna uppgradera konfigurationsparametrarna och starta uppgraderingen klusterkonfigurationen om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --klusterkonfigurationen [krävs] | Klusterkonfiguration. |
| --delta felaktiga noder | Maximalt tillåten procentandel av delta hälsa försämras under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --hälsa-check-återförsök | Hur lång tid mellan försök att utföra en kontrollerar om programmet eller klustret inte är felfri.  Som standard\: PT0H0M0S. |
| --health check stabilt | Hur lång tid att programmet eller klustret måste vara felfritt.  Som standard\: PT0H0M0S. |
| --hälsokontroll- | Hur lång tid att vänta när du har slutfört en uppgraderingsdomän innan du startar hälsotillståndet kontrollerar processen.  Som standard\: PT0H0M0S. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |
| --felaktiga program | Maximalt tillåten procentandel av felaktiga program under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --felaktiga noder | Maximalt tillåten procentandel av noder med fel under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --upgrade-domain-delta-unhealthy-nodes | Maximalt tillåten procentandel av uppgraderingsdomänen delta hälsa försämras under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --uppgraderingen domäntidsgräns | Tidsgränsen för uppgradering domänen.  Som standard\: PT0H0M0S. |
| --uppgraderingen-timeout | Tidsgränsen för uppgradering.  Som standard\: PT0H0M0S. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-kluster uppgraderingen-status
Konfigurationsstatusen klustret uppgradering av en fristående Service Fabric-klustret.

Hämta klusterkonfigurationen uppgraderingsstatus information i ett fristående Service Fabric-kluster.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
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