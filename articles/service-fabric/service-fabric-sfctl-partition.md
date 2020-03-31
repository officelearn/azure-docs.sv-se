---
title: AZURE Service Fabric CLI- sfctl-partition
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista över kommandon för att hantera partitioner för en tjänst.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: c038ef3266a727bf6984a5bd88ca540a589380db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905849"
---
# <a name="sfctl-partition"></a>sfctl partition
Fråga och hantera partitioner för alla tjänster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| förlust av data | Det här API:et inducerar dataförlust för den angivna partitionen. |
| data-förlust-status | Hämtar förloppet för en partitionsdataförluståtgärd som har startats med StartDataLoss-API:et. |
| Hälsa | Hämtar hälsotillståndet för den angivna Service Fabric-partitionen. |
| information | Hämtar information om en Service Fabric-partition. |
| lista | Hämtar listan över partitioner för en servicetjänst för tjänst. |
| läsa in | Hämtar inläsningsinformationen för den angivna Service Fabric-partitionen. |
| load-reset | Återställer den aktuella belastningen för en Service Fabric-partition. |
| kvorum-förlust | Inducerar kvorumförlust för en viss tillståndskänslig tjänstpartition. |
| kvorum-förlust-status | Hämtar förloppet för en kvorumförluståtgärd på en partition som har startats med StartQuorumLoss API.Gets the progress of a quorum loss operation on a partition started using the StartQuorumLoss API. |
| recover | Anger för service fabric-klustret att det ska försöka återställa en viss partition som för närvarande har fastnat i kvorumförlust. |
| återställa-allt | Anger för servicetygsklustret att det ska försöka återställa alla tjänster (inklusive systemtjänster) som för närvarande har fastnat i kvorumförlust. |
| rapport-hälsa | Skickar en hälsorapport på partitionen Service Fabric. |
| restart | Det här API:et startar om vissa eller alla repliker eller instanser av den angivna partitionen. |
| omstart-status | Hämtar förloppet för en PartitionRestart-åtgärd som har startats med StartPartitionRestart. |
| svc-namn | Hämtar namnet på tjänsten Service Fabric för en partition. |

## <a name="sfctl-partition-data-loss"></a>sfctl-partitionsdataförlust
Det här API:et inducerar dataförlust för den angivna partitionen.

Det utlöser ett anrop till OnDataLossAsync API för partitionen.  Det här API:et inducerar dataförlust för den angivna partitionen. Det utlöser ett anrop till OnDataLoss-API:et för partitionen. Faktisk dataförlust beror på den angivna DataLossMode.
- PartialDataLoss: Endast kvorum av repliker tas bort och OnDataLoss utlöses för partitionen men faktisk dataförlust beror på förekomsten av replikering under flygning.  
- FullDataLoss: Alla repliker tas bort varför alla data går förlorade och OnDataLoss utlöses. Detta API bör endast anropas med en tillståndskänslig tjänst som mål. Anropa detta API med en systemtjänst som målet rekommenderas inte.

> [!NOTE]   
> När det här API:et har anropats kan det inte återföras. Att anropa CancelOperation stoppar bara körningen och rensar internt systemtillstånd. Data återställs inte om kommandot har utvecklats tillräckligt långt för att orsaka dataförlust. Anropa Api:et GetDataLossProgress med samma OperationId för att returnera information om åtgärden som startades med det här API:et.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --data-loss-mode [Obligatoriskt] | Den här uppräkningen skickas till StartDataLoss API för att ange vilken typ av dataförlust som ska induceras. |
| --operation-id [Obligatoriskt] | Ett GUID som identifierar ett anrop av det här API:et.  Detta skickas till motsvarande GetProgress API. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl-partitionsdata-förlust-status
Hämtar förloppet för en partitionsdataförluståtgärd som har startats med StartDataLoss-API:et.

Hämtar förloppet för en dataförluståtgärd som startats med StartDataLoss med Hjälp av OperationId.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --operation-id [Obligatoriskt] | Ett GUID som identifierar ett anrop av det här API:et.  Detta skickas till motsvarande GetProgress API. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-health"></a>sfctl partition hälsa
Hämtar hälsotillståndet för den angivna Service Fabric-partitionen.

Använd EventsHealthStateFilter för att filtrera insamlingen av hälsohändelser som rapporterats på tjänsten baserat på hälsotillståndet. Använd ReplicasHealthStateFilter för att filtrera samlingen av ReplicaHealthState-objekt på partitionen. Om du anger en partition som inte finns i hälsoarkivet returnerar begäran ett fel.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --events-health-state-filter | Gör det möjligt att filtrera samlingen av HealthEvent-objekt som returneras baserat på hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden, som erhålls med hjälp av operatorn "ELLER". Om det angivna värdet till exempel är 6 returneras alla händelser med HealthState-värdet OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --exklude-hälso-statistik | Anger om hälsostatistiken ska returneras som en del av frågeresultatet. Falskt som standard. Statistiken visar antalet underordnade entiteter i hälsotillståndet Ok, Varning och Fel. |
| --replicas-health-state-filter | Gör det möjligt att filtrera samlingen av ReplicaHealthState-objekt på partitionen. Värdet kan erhållas från medlemmar eller bitvis åtgärder på medlemmar i HealthStateFilter. Endast repliker som matchar filtret returneras. Alla repliker används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden som erhålls med hjälp av bitvis "ELLER"-operator. Om det angivna värdet till exempel är 6 returneras alla händelser med HealthState-värdet OK (2) och Varning (4). De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd.  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-info"></a>sfctl partition info
Hämtar information om en Service Fabric-partition.

Hämtar information om den angivna partitionen. Svaret innehåller partitions-ID, partitionering schemainformation, nycklar som stöds av partitionen, status, hälsa och annan information om partitionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-list"></a>sfctl-partitionslista
Hämtar listan över partitioner för en servicetjänst för tjänst.

Svaret innehåller partitions-ID, partitionering schemainformation, nycklar som stöds av partitionen, status, hälsa och annan information om partitionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --fortsättning-token | Parametern för fortsättningstoken används för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tomt värde inkluderas i svaret på API:et när resultaten från systemet inte får plats i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API:et nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller fortsättningstoken inget värde. Värdet för den här parametern bör inte URL-kodas. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-load"></a>sfctl partition belastning
Hämtar inläsningsinformationen för den angivna Service Fabric-partitionen.

Returnerar information om inläsningen av en angiven partition. Svaret innehåller en lista över belastningsrapporter för en Service Fabric-partition. Varje rapport innehåller inläsningsmåttets namn, värde och senast rapporterade tid i UTC.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-load-reset"></a>sfctl partition load-reset
Återställer den aktuella belastningen för en Service Fabric-partition.

Återställer den aktuella belastningen för en Service Fabric-partition till standardbelastningen för tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partition kvorum-förlust
Inducerar kvorumförlust för en viss tillståndskänslig tjänstpartition.

Det här API:et är användbart för en tillfällig kvorumförlustsituation på tjänsten. Anropa Api:et GetQuorumLossProgress med samma OperationId för att returnera information om åtgärden som startades med det här API:et. Detta kan bara anropas på tillståndskänsliga beständiga (HasPersistedState==true) tjänster.  Använd inte det här API:et på tillståndslösa tjänster eller tillståndskänsliga tjänster i minnet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --operation-id [Obligatoriskt] | Ett GUID som identifierar ett anrop av det här API:et.  Detta skickas till motsvarande GetProgress API. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --quorum-förlust-varaktighet [Obligatoriskt] | Hur lång tid partitionen ska hållas i kvorumförlust.  Detta måste anges i sekunder. |
| --quorum-loss-mode [Obligatoriskt] | Den här uppräkningen skickas till StartQuorumLoss API för att ange vilken typ av kvorumförlust som ska induceras. |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partition kvorum-förlust-status
Hämtar förloppet för en kvorumförluståtgärd på en partition som har startats med StartQuorumLoss API.Gets the progress of a quorum loss operation on a partition started using the StartQuorumLoss API.

Får förloppet för en kvorumförluståtgärd som startats med StartQuorumLoss med hjälp av den angivna OperationId.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --operation-id [Obligatoriskt] | Ett GUID som identifierar ett anrop av det här API:et.  Detta skickas till motsvarande GetProgress API. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-recover"></a>sfctl partition återställa
Anger för service fabric-klustret att det ska försöka återställa en viss partition som för närvarande har fastnat i kvorumförlust.

Den här åtgärden bör endast utföras om det är känt att replikerna som är nere inte kan återställas. Felaktig användning av detta API kan orsaka potentiell dataförlust.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-recover-all"></a>sfctl partition återställa-alla
Anger för servicetygsklustret att det ska försöka återställa alla tjänster (inklusive systemtjänster) som för närvarande har fastnat i kvorumförlust.

Den här åtgärden bör endast utföras om det är känt att replikerna som är nere inte kan återställas. Felaktig användning av detta API kan orsaka potentiell dataförlust.

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

## <a name="sfctl-partition-report-health"></a>sfctl partition rapport-hälsa
Skickar en hälsorapport på partitionen Service Fabric.

Rapporterar hälsotillståndet för den angivna Service Fabric-partitionen. Rapporten måste innehålla information om källan till hälsorapporten och egenskapen som den rapporteras till. Rapporten skickas till en service fabric gateway-partition, som vidarebefordras till hälsoarkivet. Rapporten kan accepteras av gatewayen, men avvisas av hälsoarkivet efter extra validering. Hälsoarkivet kan till exempel avvisa rapporten på grund av en ogiltig parameter, till exempel ett inaktuellt sekvensnummer. Om du vill se om rapporten har tillämpats i hälsoarkivet kontrollerar du att rapporten visas i avsnittet händelser.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --health-property [Obligatoriskt] | Egenskapen av hälsoinformationen. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning för att tillåta reporterflexibilitet att kategorisera tillståndsvillkoret som utlöser rapporten. En reporter med SourceId "LocalWatchdog" kan till exempel övervaka tillståndet för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på den noden. Samma reporter kan övervaka nodanslutningen, så att den kan rapportera en egenskap "Anslutning" på samma nod. I hälsoarkivet behandlas dessa rapporter som separata hälsohändelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen hälsoinformationen unikt. |
| --hälsotillstånd [Krävs] | Möjliga värden\: är "Ogiltig", "Ok", "Varning", "Fel", "Okänd". |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --source-id [Obligatoriskt] | Källnamnet som identifierar komponenten klient/watchdog/system som genererade hälsoinformationen. |
| --beskrivning | Beskrivningen av hälsoinformationen. <br><br> Den representerar fritext som används för att lägga till läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4096 tecken. Om den angivna strängen är längre trunkeras den automatiskt. När de sista tecknen i beskrivningen trunkeras innehåller de sista tecknen i beskrivningen en markör "[Trunkerad]" och den totala strängstorleken 4096 tecken. Förekomsten av markören anger för användarna att trunkering inträffade. Observera att när beskrivningen trunkeras har den mindre än 4096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till ett service fabric-gatewayprogram som vidarebefordras till hälsoarkivet. Om Omedelbar är inställd på true skickas rapporten omedelbart från HTTP Gateway till hälsoarkivet, oavsett de inställningar för fabric-klient som HTTP Gateway-programmet använder. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor kan det fortfarande misslyckas att skicka rapporten, till exempel om HTTP-gatewayen är stängd eller om meddelandet inte når gatewayen. Om Omedelbar är inställd på false skickas rapporten baserat på hälsoklientinställningarna från HTTP Gateway. Därför kommer den att batchas enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom hälsoklienten kan optimera hälsorapporteringsmeddelanden till hälsoarkivet samt bearbetning av hälsorapporter. Som standard skickas inte rapporter omedelbart. |
| --remove-when-expired --remove-when-expired --remove-when-expired -- | Värde som anger om rapporten tas bort från hälsoarkivet när den upphör att gälla. <br><br> Om värdet är true tas rapporten bort från hälsoarkivet när den har upphört att gälla. Om den är inställd på false behandlas rapporten som ett fel när den har upphört att gälla. Värdet för den här egenskapen är falskt som standard. När klienter rapporterar regelbundet bör de ange RemoveWhenExpired false (standard). På så sätt har reportern problem (t.ex. dödläge) och kan inte rapportera, entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som i felhälsotillstånd. |
| --sekvens-nummer | Sekvensnumret för den här hälsorapporten som en numerisk sträng. <br><br> Rapportsekvensnumret används av hälsoarkivet för att identifiera inaktuella rapporter. Om inget anges genereras ett sekvensnummer automatiskt av hälsoklienten när en rapport läggs till. |
| --timeout -t | Standard\: 60. |
| --ttl | Den varaktighet för vilken hälsorapporten är giltig. I det här fältet används ISO8601-format för att ange varaktigheten. <br><br> När klienter rapporterar regelbundet bör de skicka rapporter med högre frekvens än tid att leva. Om klienter rapporterar om övergången kan de ange att tiden ska vara oändlig. När tiden för att leva går ut tas hälsohändelsen som innehåller hälsoinformationen antingen bort från hälsoarkivet, om RemoveWhenExpired är sant eller utvärderas vid fel, om RemoveWhenExpired false. Om inget anges kan tid att leva som standard oändligt värde. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-restart"></a>sfctl partition omstart
Det här API:et startar om vissa eller alla repliker eller instanser av den angivna partitionen.

Det här API:et är användbart för att testa redundans. Om den används för att rikta in sig på en tillståndslös tjänstpartition måste RestartPartitionMode vara AllReplicasOrInstances. Anropa Api:et GetPartitionRestartProgress med samma OperationId för att hämta förloppet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --operation-id [Obligatoriskt] | Ett GUID som identifierar ett anrop av det här API:et.  Detta skickas till motsvarande GetProgress API. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --restart-partition-mode [Obligatoriskt] | Beskriv vilka partitioner som ska startas om. |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-restart-status"></a>sfctl partition omstart-status
Hämtar förloppet för en PartitionRestart-åtgärd som har startats med StartPartitionRestart.

Hämtar förloppet för en PartitionRestart som startas med StartPartitionRestart med den medföljande OperationId.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --operation-id [Obligatoriskt] | Ett GUID som identifierar ett anrop av det här API:et.  Detta skickas till motsvarande GetProgress API. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-partition-svc-name"></a>sfctl partition svc-namn
Hämtar namnet på tjänsten Service Fabric för en partition.

Hämtar namnet på tjänsten för den angivna partitionen. Ett 404-fel returneras om partitions-ID:n inte finns i klustret.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
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
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).
