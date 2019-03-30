---
title: Azure Service Fabric CLI - sfctl sa-kluster | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl fristående klusterkommandon.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: a652439729e538b3ce2545ab3b09284e6645ce9d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668525"
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
| --configuration-api-version [Required] | API-versionen av fristående json klusterkonfigurationen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
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
| --delta-unhealthy-nodes | Högsta tillåtna procentandel förändrade hälsotillstånd försämring under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --health-check-retry | Hur lång tid mellan försök att utföra hälsokontroller om programmet eller klustret inte är felfri.  Standard\: PT0H0M0S. |
| --health-check-stable | Hur lång tid att programmet eller klustret måste vara felfria innan uppgraderingen fortsätter du med nästa uppgraderingsdomän.  Standard\: PT0H0M0S. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --health-check-wait | Hur lång tid att vänta efter att du har slutfört en uppgraderingsdomän innan du startar hälsotillståndet kontrollerar processen.  Standard\: PT0H0M0S. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --unhealthy-applications | Högsta tillåtna procentandel program som är felaktiga under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --defekta noder | Högsta tillåtna procentandel av defekta noder under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --upgrade-domain-delta-unhealthy-nodes | Högsta tillåtna procentandel uppgraderingsdomän delta hälsotillstånd försämring under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --upgrade-domain-timeout | Hur lång tid varje domän har slutförts innan FailureAction körs.  Standard\: PT0H0M0S. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --upgrade-timeout | Hur lång tid övergripande uppgraderingen har slutförts innan FailureAction körs.  Standard\: PT0H0M0S. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
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

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).