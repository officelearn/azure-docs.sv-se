---
title: Azure Service Fabric CLI - sfctl-partition | Microsoft Docs
description: Beskriver Service Fabric CLI-kommandon sfctl partition.
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
ms.openlocfilehash: f7c9bcc51757100cb1fc957dee12213bc8bf2eec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556615"
---
# <a name="sfctl-partition"></a>sfctl partition
Fråga och hantera partitioner för alla tjänster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| förlust av data | Detta API kan orsaka förlust av data för den angivna partitionen. |
| data-loss-status | Hämtar förloppet för en partition förlust dataåtgärd igång med hjälp av StartDataLoss-API. |
| hälsa | Hämtar hälsotillståndet för den angivna Service Fabric-partitionen. |
| info | Hämtar information om en Service Fabric-partition. |
| lista | Hämtar listan över partitioner för en Service Fabric-tjänst. |
| läsa in | Hämtar information belastningen av den angivna Service Fabric-partitionen. |
| load-reset | Återställer den aktuella belastningen för en Service Fabric-partition. |
| quorum-loss | Startar förlorar kvorum för en viss tillståndskänslig tjänst-partition. |
| quorum-loss-status | Hämtar förloppet för en kvorum förlust-åtgärd i en partition som startats med hjälp av StartQuorumLoss-API. |
| återställa | Anger att den ska försöka att återställa en specifik partition som för närvarande har fastnat förlorar kvorum till Service Fabric-klustret. |
| recover-all | Anger att den ska försöka att återställa alla tjänster (inklusive systemtjänster) som för närvarande har fastnat i förlorar kvorum till Service Fabric-klustret. |
| rapportera hälsa | Skickar en hälsorapport på Service Fabric-partitionen. |
| restart | Detta API startar om vissa eller alla repliker eller instanser av den angivna partitionen. |
| omstart-status | Hämtar en PartitionRestart åtgärdens komma igång med StartPartitionRestart förlopp. |
| svc-name | Hämtar namnet på Service Fabric-tjänst för en partition. |

## <a name="sfctl-partition-data-loss"></a>sfctl partition dataförlust
Detta API kan orsaka förlust av data för den angivna partitionen.

Den utlöser ett anrop till OnDataLossAsync-API: et för partitionen.  Detta API kan orsaka förlust av data för den angivna partitionen. Den utlöser ett anrop till OnDataLoss-API: et för partitionen. Faktiska data går förlorade beror på den angivna DataLossMode.  <br> -PartialDataLoss - ett kvorum av repliker tas bort och OnDataLoss utlöses för partitionen men faktiska dataförlust beror på förekomsten av pågående replikering.  <br> -FullDataLoss - alla repliker har tagits bort kan därför alla data går förlorad och OnDataLoss utlöses. Detta API bör endast anropas med en tillståndskänslig tjänst som mål. Det rekommenderas inte att anropa detta API med en systemtjänst som mål.

> [!NOTE] 
> När detta API har anropats kan inte ångras. Anropa CancelOperation bara stoppa körning och rensa interna system-tillstånd. Det kan inte återställa data om kommandot har utvecklats tillräckligt långt kan orsaka förlust av data. Anropa API GetDataLossProgress med samma OperationId att returnera information om hur igång med detta API.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --data går förlorade-läge [krävs] | Den här uppräkningen har överförts till StartDataLoss-API för att ange vilken typ av dataförlust orsaka. |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta skickas vidare till motsvarande GetProgress API. |
| --partitions-id [krävs] | Identiteten för partitionen. |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partition data-förlust-status
Hämtar förloppet för en partition förlust dataåtgärd igång med hjälp av StartDataLoss-API.

Hämtar förloppet för en åtgärd för förlust av data som utgick StartDataLoss, med hjälp av åtgärds-ID.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta skickas vidare till motsvarande GetProgress API. |
| --partition-id [Required] | Identiteten för partitionen. |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-health"></a>sfctl partition health
Hämtar hälsotillståndet för den angivna Service Fabric-partitionen.

Använd EventsHealthStateFilter för att filtrera insamling av hälsotillståndshändelser för tjänsten baserat på hälsotillståndet rapporteras. Använd ReplicasHealthStateFilter för att filtrera objektsamlingen ReplicaHealthState på partitionen. Om du anger en partition som inte finns i health store, returneras ett fel i den här begäran.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identiteten för partitionen. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --exclude-health-statistics | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel. |
| --replicas-health-state-filter | Tillåter filtrering objektsamlingen ReplicaHealthState på partitionen. Värdet kan hämtas från medlemmar eller bitvis åtgärder på medlemmar i HealthStateFilter. Repliker som matchar filtret kommer att returneras. Alla repliker som används för att utvärdera aggregerade hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 returneras sedan alla händelser med HealthState värdet OK (2) och varning (4). De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-info"></a>sfctl partition info
Hämtar information om en Service Fabric-partition.

Hämtar information om den angivna partitionen. Svaret innehåller partitions-ID, partitionering schema information, nycklar som stöds av partitionen, status, hälsotillstånd och annan information om partitionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Required] | Identiteten för partitionen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-list"></a>sfctl Partitionslista
Hämtar listan över partitioner för en Service Fabric-tjänst.

Svaret innehåller partitions-ID, partitionering schema information, nycklar som stöds av partitionen, status, hälsotillstånd och annan information om partitionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --continuation-token | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-load"></a>sfctl partitionsinläsning
Hämtar information belastningen av den angivna Service Fabric-partitionen.

Returnerar information om belastningen på en angiven partition. Svaret innehåller en lista över belastningen rapporter för en Service Fabric-partition. Varje rapport innehåller belastningen Måttnamn, värdet och senaste rapporterad tid i UTC.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Required] | Identiteten för partitionen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-load-reset"></a>sfctl partition load-återställning
Återställer den aktuella belastningen för en Service Fabric-partition.

Återställer den aktuella belastningen för en Service Fabric-partition på belastningen som standard för tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Required] | Identiteten för partitionen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-quorum-loss"></a>förlorar med sfctl partition kvorum
Startar förlorar kvorum för en viss tillståndskänslig tjänst-partition.

Detta API är användbart för en tillfällig kvorum förlust situation på din tjänst. Anropa API GetQuorumLossProgress med samma OperationId att returnera information om hur igång med detta API. Detta kan endast anropas på tillståndskänslig beständiga (HasPersistedState == true) tjänster.  Använd inte detta API om tillståndslösa tjänster och tillståndskänsliga InMemory-endast tjänster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta skickas vidare till motsvarande GetProgress API. |
| --partitions-id [krävs] | Identiteten för partitionen. |
| --kvorum förlust-varaktighet [krävs] | Hur lång tid som sparas partitionen förlorar kvorum.  Detta måste anges i sekunder. |
| --kvorum förlust-läge [krävs] | Den här uppräkningen har överförts till StartQuorumLoss-API för att ange vilken typ av förlorar kvorum orsaka. |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partition kvorum-förlust-status
Hämtar förloppet för en kvorum förlust-åtgärd i en partition som startats med hjälp av StartQuorumLoss-API.

Hämtar förloppet för en åtgärd för förlust av kvorum igång med StartQuorumLoss, med hjälp av det tillhandahållna åtgärds-ID.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta skickas vidare till motsvarande GetProgress API. |
| --partition-id [Required] | Identiteten för partitionen. |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-recover"></a>sfctl partition återställa
Anger att den ska försöka att återställa en specifik partition som för närvarande har fastnat förlorar kvorum till Service Fabric-klustret.

Den här åtgärden bör endast utföras om det är känt att replikerna är nere inte kan återställas. Felaktig användning av detta API kan orsaka förlust av data.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Required] | Identiteten för partitionen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-recover-all"></a>sfctl partition Återställ alla
Anger att den ska försöka att återställa alla tjänster (inklusive systemtjänster) som för närvarande har fastnat i förlorar kvorum till Service Fabric-klustret.

Den här åtgärden bör endast utföras om det är känt att replikerna är nere inte kan återställas. Felaktig användning av detta API kan orsaka förlust av data.

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

## <a name="sfctl-partition-report-health"></a>sfctl partition report-health
Skickar en hälsorapport på Service Fabric-partitionen.

Rapporterar hälsotillståndet för den angivna Service Fabric-partitionen. Rapporten måste innehålla information om orsaken hälsorapport och egenskapen som har rapporterats. Rapporten skickas till en Partition som vidarebefordrar till arkivet hälsa för Service Fabric gateway. Rapporten kan accepteras av gatewayen, men avvisats av health store efter extra valideringen. Health store kan till exempel avvisa rapporten på grund av en ogiltig parameter, t.ex. ett sekvensnummer som är inaktuella. Kontrollera att rapporten ska visas i avsnittet händelser för att se om rapporten har tillämpats i health store.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --health-egenskapen [krävs] | Egenskapen för hälsoinformation. <br><br> En entitet kan ha rapporter om hälsotillstånd för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport flexibiliteten att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan till exempel övervaka status för tillgängliga disken på en nod, så att den kan rapportera ”AvailableDisk”-egenskap på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. Dessa rapporter behandlas i health store, som separata health-händelser för den angivna noden. Tillsammans med målentiteten identifierar egenskapen hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden omfattar\: ”ogiltig”, ”Ok”, ”varning”, ”fel”, ”okänd”. |
| --partitions-id [krävs] | Identiteten för partitionen. |
| --käll-id [krävs] | Källnamn som identifierar/klientsystemet/watchdog-komponenten som genererade hälsoinformation. |
| – Beskrivning | Beskrivning av hälsoinformation. <br><br> Den motsvarar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4 096 tecken. Om strängen inte trunkeras den automatiskt. När trunkeras, de sista tecknen i beskrivningen innehåller en markör ”[trunkerat]” och totala storleken på målsträngen är 4 096 tecken. Förekomst av markören anger för användare att trunkering inträffade. Observera att när trunkeras, beskrivningen har mindre än 4 096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till en Service Fabric gateway programmet, som vidarebefordrar till health store. Om Immediate anges till SANT, skickas rapporten direkt från HTTP-Gateway till health store, oavsett inställningarna i fabric-klient som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas skicka rapporten fortfarande, till exempel om HTTP-Gateway är stängd eller meddelandet når inte gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningarna hälsotillstånd från HTTP-Gateway. Det kan därför batchhanteras enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom den tillåter hälsotillstånd klienten att optimera reporting meddelanden hälsoarkivet, samt för bearbetning av hälsotillstånd. Som standard skickas rapporter inte omedelbart. |
| --remove-when-expired | Värde som anger om rapporten tas bort från health store när den upphör att gälla. <br><br> Om värdet är true, rapporten tas bort från health store när den upphör att gälla. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). På så sätt kan är personen har problem (t.ex. deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
| ---sekvensnummer | Sekvensnumret för den här hälsorapport som en numerisk sträng. <br><br> Sekvensnumret rapporten används av health store för att identifiera inaktuella rapporter. Om inte anges genereras ett sekvensnummer automatiskt av hälsotillstånd klienten när du lägger till en rapport. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --ttl | Den tid som den här hälsorapport är giltig. Det här fältet använder ISO8601-format för att ange varaktigheten. <br><br> När klienterna rapporterar med jämna mellanrum, bör de skicka rapporter med frekvens som är högre än TTL-värde. Om klienterna rapporterar på övergång, kan de ange time to live till obegränsad. När TTL-värde upphör att gälla hälsohändelsen som innehåller hälsoinformation är antingen tas bort från health store om RemoveWhenExpired är true och utvärderas vid fel, om RemoveWhenExpired false. Om inte tidpunkten TTL-värde standardvärdet är oändligt värde. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-restart"></a>sfctl partition omstart
Detta API startar om vissa eller alla repliker eller instanser av den angivna partitionen.

Detta API är användbart för att testa redundans. Om används för att rikta en tillståndslös tjänstpartition, vara RestartPartitionMode AllReplicasOrInstances. Anropa API GetPartitionRestartProgress med samma OperationId för att få förloppet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta skickas vidare till motsvarande GetProgress API. |
| --partitions-id [krävs] | Identiteten för partitionen. |
| --omstart partition-läge [krävs] | Beskriv vilka partitioner för att starta om. |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-restart-status"></a>sfctl partition omstart-status
Hämtar en PartitionRestart åtgärdens komma igång med StartPartitionRestart förlopp.

Hämtar förloppet för en PartitionRestart igång med StartPartitionRestart med hjälp av det tillhandahållna åtgärds-ID.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta skickas vidare till motsvarande GetProgress API. |
| --partition-id [Required] | Identiteten för partitionen. |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-partition-svc-name"></a>sfctl svc-partitionsnamn
Hämtar namnet på Service Fabric-tjänst för en partition.

Hämtar namnet på tjänsten för den angivna partitionen. Ett 404-fel returneras om partitions-ID inte finns i klustret.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Required] | Identiteten för partitionen. |
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
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
