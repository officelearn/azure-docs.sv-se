---
title: Azure Service Fabric CLI- sfctl sa-cluster
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för hantering av fristående kluster.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 233148c04fb1a9c6cf1d6c7042c12c54eebd0205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904919"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Hantera fristående Service Fabric-kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Config | Hämta service fabric-en fristående klusterkonfiguration. |
| config-uppgradering | Börja uppgradera konfigurationen av ett fristående service fabric-kluster. |
| uppgraderingsstatus | Hämta status för uppgradering av klusterkonfiguration för ett fristående service fabric-kluster. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-kluster config
Hämta service fabric-en fristående klusterkonfiguration.

Klusterkonfigurationen innehåller egenskaper för klustret som innehåller olika nodtyper i klustret, säkerhetskonfigurationer, fel och uppgradera domäntopologier osv.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --configuration-api-version [Obligatoriskt] | API-versionen av den fristående klusterjisonkonfigurationen. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade sfctl sa-cluster config-upgrade sfctl sa-cluster config-upgrade sfc
Börja uppgradera konfigurationen av ett fristående service fabric-kluster.

Validera de medföljande konfigurationsuppgraderingsparametrarna och börja uppgradera klusterkonfigurationen om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --cluster-config [Krävs] | Klusterkonfigurationen. |
| --ansökan-hälso-politik | JSON kodade ordlista med par av programtypsnamn och maximal procentandel som var felaktig innan felupptäckt. |
| --delta-ohälsosam-noder | Den högsta tillåtna procentandelen delta hälsoförsämring under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --hälsokontroll-nyttförsök | Hur lång tid det går mellan försök att utföra hälsokontroller om programmet eller klustret inte är felfritt.  Standard\: PT0H0M0S. |
| --hälsokontroll-stabil | Den tid som programmet eller klustret måste förbli felfritt innan uppgraderingen fortsätter till nästa uppgraderingsdomän.  Standard\: PT0H0M0S. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälsa-check-vänta | Hur lång tid du ska vänta efter att ha slutfört en uppgraderingsdomän innan hälsokontrollprocessen startas.  Standard\: PT0H0M0S. |
| --timeout -t | Standard\: 60. |
| --ohälsosamma-applikationer | Den högsta tillåtna procentandelen felaktiga program under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --ohälsosam-noder | Den högsta tillåtna procentandelen felaktiga noder under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --upgrade-domain-delta-unhealthy-noder | Den högsta tillåtna procentandelen uppgraderingsdomändeltathälsaförsämring under uppgraderingen. Tillåtna värden är heltalsvärden från noll till 100. |
| --upgrade-domain-timeout --upgrade-domain-timeout --upgrade-domain-timeout -- | Den tid som varje uppgraderingsdomän måste slutföra innan FailureAction körs.  Standard\: PT0H0M0S. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --upgrade-timeout --upgrade-timeout --upgrade-timeout -- | Hur lång tid den totala uppgraderingen måste slutföras innan FailureAction körs.  Standard\: PT0H0M0S. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

### <a name="examples"></a>Exempel

Starta en uppdatering av klusterkonfigurationen
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Hämta status för uppgradering av klusterkonfiguration för ett fristående service fabric-kluster.

Hämta statusinformation för klusterkonfigurationsuppgradering för ett fristående service fabric-kluster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).