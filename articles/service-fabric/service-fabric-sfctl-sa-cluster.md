---
title: Azure Service Fabric CLI - sfctl sa-kluster | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl fristående klusterkommandon.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: d7f33bf0657ca2a6888387b7651706f9de537bb4
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494364"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Hantera fristående Service Fabric-kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| config | Hämta Service Fabric fristående klusterkonfigurationen. |
| config-uppgradering | Börja uppdatera konfigurationen av en fristående Service Fabric-kluster. |
| uppgraderingen-status | Konfigurationsstatusen kluster uppgradering av en fristående Service Fabric-kluster. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Hämta Service Fabric fristående klusterkonfigurationen.

Klusterkonfigurationen innehåller egenskaper för klustret som innehåller olika nodtyper på klustret, säkerhetskonfigurationer, fel, och uppgraderingsdomän topologier, osv.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --configuration-api-versionen [krävs] | API-versionen av fristående json klusterkonfigurationen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-uppgradering
Börja uppdatera konfigurationen av en fristående Service Fabric-kluster.

Verifiera Uppgraderingsparametrar för angiven konfiguration och börja uppgradera klusterkonfigurationen om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --kluster-config [krävs] | Klusterkonfigurationen som ska kopplas till klustret. |
| --programmet hälsoprinciper | JSON-kodad ordlista med par med programtypnamn och Maxprocent defekta innan du höjer fel. |
| --förändrade defekta noder | Högsta tillåtna procentandel förändrade hälsotillstånd försämring under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --hälsotillstånd – Kontrollera-försök | Hur lång tid mellan försöken att utföra en kontrollerar om programmet eller klustret inte är felfri.  Standard\: PT0H0M0S. |
| --hälsotillstånd – Kontrollera-stabil | Hur lång tid att programmet eller klustret måste vara felfritt.  Standard\: PT0H0M0S. |
| --hälsotillstånd – Kontrollera-vänta | Hur lång tid att vänta efter att du har slutfört en uppgraderingsdomän innan du startar hälsotillståndet kontrollerar processen.  Standard\: PT0H0M0S. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --felaktiga program | Högsta tillåtna procentandel program som är felaktiga under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --defekta noder | Högsta tillåtna procentandel av defekta noder under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --upgrade-domain-delta-unhealthy-nodes | Högsta tillåtna procentandel uppgraderingsdomän delta hälsotillstånd försämring under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --uppgraderingen domäntidsgräns | Tidsgränsen för uppgraderingsdomänen.  Standard\: PT0H0M0S. |
| --Tidsgräns för uppgradering | Tidsgränsen för uppgradering.  Standard\: PT0H0M0S. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

### <a name="examples"></a>Exempel

Starta en konfiguration update sfctl sa-cluster config-uppgradering av klustret--kluster-config <YOUR CLUSTER CONFIG> --programmet hälsoprinciper ”{” fabric: / System ”: {” ConsiderWarningAsError ”: true}}”

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster uppgraderingen-status
Konfigurationsstatusen kluster uppgradering av en fristående Service Fabric-kluster.

Klusterkonfigurationen uppgradera information om Erhåll status för ett fristående Service Fabric-kluster.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).