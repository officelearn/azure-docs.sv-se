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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: ce10e2c24e89140357df3fa6b724a1f89f389a50
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275490"
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

|Argument|Beskrivning|
| --- | --- |
| --configuration-api-versionen [krävs] | API-versionen av fristående json klusterkonfigurationen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
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

|Argument|Beskrivning|
| --- | --- |
| --kluster-config [krävs] | Klusterkonfigurationen. |
| --programmet hälsoprinciper | JSON-kodad ordlista med par med programtypnamn och Maxprocent defekta innan du höjer fel. |
| --förändrade defekta noder | Högsta tillåtna procentandel förändrade hälsotillstånd försämring under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --hälsotillstånd – Kontrollera-försök | Hur lång tid mellan försök att utföra hälsokontroller om programmet eller klustret inte är felfri.  Standard\: PT0H0M0S. |
| --hälsotillstånd – Kontrollera-stabil | Hur lång tid att programmet eller klustret måste vara felfria innan uppgraderingen fortsätter du med nästa uppgraderingsdomän.  Standard\: PT0H0M0S. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälsotillstånd – Kontrollera-vänta | Hur lång tid att vänta efter att du har slutfört en uppgraderingsdomän innan du startar hälsotillståndet kontrollerar processen.  Standard\: PT0H0M0S. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --felaktiga program | Högsta tillåtna procentandel program som är felaktiga under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --defekta noder | Högsta tillåtna procentandel av defekta noder under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --upgrade-domain-delta-unhealthy-nodes | Högsta tillåtna procentandel uppgraderingsdomän delta hälsotillstånd försämring under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --uppgraderingen domäntidsgräns | Hur lång tid varje domän har slutförts innan FailureAction körs.  Standard\: PT0H0M0S. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --Tidsgräns för uppgradering | Hur lång tid övergripande uppgraderingen har slutförts innan FailureAction körs.  Standard\: PT0H0M0S. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |

### <a name="global-arguments"></a>Global argument

|Argument|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

### <a name="examples"></a>Exempel

Starta en uppdatering för konfiguration av kluster

```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster uppgraderingen-status
Konfigurationsstatusen kluster uppgradering av en fristående Service Fabric-kluster.

Klusterkonfigurationen uppgradera information om Erhåll status för ett fristående Service Fabric-kluster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

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