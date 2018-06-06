---
title: Azure Service Fabric CLI - sfctl partition | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl partition-kommandon.
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
ms.openlocfilehash: a9455683c5fad7fad4dda62fd967da617d8a8496
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763654"
---
# <a name="sfctl-partition"></a>sfctl partition
Fråga efter och hantera partitioner för alla tjänster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| förlust av data | Detta API kan orsaka förlust av data för den angivna partitionen. |
| status för data går förlorade | Hämtar partition data går förlorade åtgärdens igång med API: et StartDataLoss förlopp. |
| hälsotillstånd | Hämtar hälsotillståndet för den angivna Service Fabric-partitionen. |
| info | Hämtar information om en Service Fabric-partition. |
| lista | Hämtar listan över partitioner i en Service Fabric-tjänst. |
| läsa in | Systemklockan belastningen av den angivna Service Fabric-partitionen. |
| Läs in återställning | Återställer den aktuella belastningen för Service Fabric-partitionen. |
| förlorar kvorum | Startar förlorar kvorum för en given tillståndskänslig service-partition. |
| kvorum-förlust-status | Hämtar status för ett kvorum förlust åtgärden på en partition som startats med hjälp av StartQuorumLoss-API. |
| Återställ | Anger att den ska försöka att återställa en specifik partition som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret. |
| Återställ alla | Anger att den ska försöka att återställa några tjänster (inklusive systemtjänster) som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret. |
| rapporten hälsa | Skickar en hälsorapport i Service Fabric-partitionen. |
| Starta om | Detta API startar om vissa eller alla repliker eller instanser av den angivna partitionen. |
| Starta om-status | Hämtar en PartitionRestart åtgärdens igång med StartPartitionRestart förlopp. |
| SVC-namn | Hämtar namnet på Service Fabric-tjänst för en partition. |

## <a name="sfctl-partition-data-loss"></a>sfctl partition dataförlust
Detta API kan orsaka förlust av data för den angivna partitionen.

Den utlöser ett anrop till OnDataLossAsync-API: et för partitionen.  Detta API kan orsaka förlust av data för den angivna partitionen. Den utlöser ett anrop till OnDataLoss-API: et för partitionen. Verklig dataförlust beror på den angivna DataLossMode. <br> PartialDataLoss - ett kvorum av repliker tas bort och OnDataLoss utlöses för partitionen men faktiska dataförluster beror på förekomsten av pågående replikering. <br>FullDataLoss - alla repliker är bort därför alla data går förlorade och OnDataLoss utlöses. <br>Detta API bör endast anropas med en tillståndskänslig tjänst som mål. Det rekommenderas inte att anropa denna API med en systemtjänst som mål. 
> [!NOTE]
> När detta API har anropats, kan inte ångras. Anropar CancelOperation bara stoppa körning och rensa interna systemtillstånd. Den kan inte återställa data om kommandot har nått tillräckligt långt data gå förlorade. Anropa GetDataLossProgress-API med samma åtgärds-ID att returnera information om hur igång med den här API.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --data går förlorade-läge [krävs] | Den här uppräkningen har överförts till StartDataLoss-API för att ange vilken typ av orsaka förlust av data. |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --tjänst-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partition data-förlust-status
Hämtar partition data går förlorade åtgärdens igång med API: et StartDataLoss förlopp.

Hämtar status för en åtgärd för förlust av data igång med StartDataLoss, med hjälp av åtgärds-ID.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --tjänst-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-health"></a>sfctl partition hälsa
Hämtar hälsotillståndet för den angivna Service Fabric-partitionen.

Hämtar information om hälsa för den angivna partitionen. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillstånd händelser som rapporterats för tjänsten baserat på hälsotillståndet. Använd ReplicasHealthStateFilter för att filtrera objektsamlingen ReplicaHealthState på partitionen. Om du anger en partition som inte finns i health store, returneras ett fel i den här förfrågan.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identitet för partitionen. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --Utelämna hälsostatistik | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel. |
| --replicas-health-state-filter | Tillåter filtrering objektsamlingen ReplicaHealthState på partitionen. Värdet kan hämtas från medlemmar eller binär åtgärder på medlemmar i HealthStateFilter. Repliker som matchar filtret kommer att returneras. Alla repliker används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 ska sedan alla händelser med HealthState värdet OK (2) och varning (4) returneras. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-info"></a>sfctl partition info
Hämtar information om en Service Fabric-partition.

Hämtar information om den angivna partitionen. Svaret innehåller partitions-ID, information om partitionering schemat, nycklar som stöds av partitionen, status, hälsotillstånd och annan information om partitionen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identitet för partitionen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-list"></a>sfctl Partitionslista
Hämtar listan över partitioner i en Service Fabric-tjänst.

Hämtar listan över partitioner i en Service Fabric-tjänst. Svaret innehåller partitions-ID, information om partitionering schemat, nycklar som stöds av partitionen, status, hälsotillstånd och annan information om partitionen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --fortsättningstoken | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett tomt värde inkluderas i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-load"></a>sfctl partitionsbelastning
Systemklockan belastningen av den angivna Service Fabric-partitionen.

Returnerar information om belastningen på en angiven partition. Svaret innehåller en lista över belastningen rapporter för ett Service Fabric-partitionen. Varje rapport innehåller mått namn, värde och rapporterade senast i UTC.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identitet för partitionen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-load-reset"></a>sfctl partition belastningen återställning
Återställer den aktuella belastningen för Service Fabric-partitionen.

Återställer den aktuella belastningen för Service Fabric-partitionen till standard-belastningen för tjänsten.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identitet för partitionen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partition kvorumförlust
Startar förlorar kvorum för en given tillståndskänslig service-partition.

Startar förlorar kvorum för en given tillståndskänslig service-partition.  Detta API är användbart för ett tillfälligt kvorum förlust situationen på din tjänst. Anropa GetQuorumLossProgress-API med samma åtgärds-ID att returnera information om hur igång med den här API. Detta kan endast anropas på stateful beständiga (HasPersistedState == true) tjänster.  Använd inte denna API på tillståndslösa tjänster eller tillståndskänslig i minnet enbart tjänster.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --kvorum förlust-varaktighet [krävs] | Hur lång tid som sparas partitionen förlorar kvorum.  Detta måste anges i sekunder. |
| --kvorum förlust-läge [krävs] | Den här uppräkningen har överförts till StartQuorumLoss-API för att ange vilken typ av förlorar kvorum orsaka. |
| --tjänst-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partition kvorum-förlust-status
Hämtar status för ett kvorum förlust åtgärden på en partition som startats med hjälp av StartQuorumLoss-API.

Hämtar status för en åtgärd för förlust av kvorum igång med StartQuorumLoss, med hjälp av det tillhandahållna åtgärds-ID.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --tjänst-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-recover"></a>återställa sfctl partition
Anger att den ska försöka att återställa en specifik partition som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret.

Anger att den ska försöka att återställa en specifik partition som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret. Den här åtgärden bör bara utföras om det är känt att replikerna är nere inte kan återställas. Felaktig användning av denna API kan orsaka förlust av data.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identitet för partitionen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-recover-all"></a>sfctl partition Återställ alla
Anger att den ska försöka att återställa några tjänster (inklusive systemtjänster) som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret.

Anger att den ska försöka att återställa några tjänster (inklusive systemtjänster) som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret. Den här åtgärden bör bara utföras om det är känt att replikerna är nere inte kan återställas. Felaktig användning av denna API kan orsaka förlust av data.

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

## <a name="sfctl-partition-report-health"></a>sfctl partition rapport-hälsa
Skickar en hälsorapport i Service Fabric-partitionen.

Rapporterar hälsotillståndet för den angivna Service Fabric-partitionen. Rapporten innehålla information om källan för hälsorapport och egenskapen som har rapporterats. Rapporten skickas till Service Fabric-gateway Partition som vidarebefordrar till health store. Rapporten kan accepteras av gatewayen men avvisas av health store efter extra validering. Health store kan avvisa rapporten på grund av en ogiltig parameter som ett inaktuella sekvensnummer. Kontrollera att rapporten ska visas i avsnittet händelser för att se om rapporten har tillämpats i health store.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --hälsa egenskapen [krävs] | Egenskapen för information om hälsa. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport möjlighet att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan övervaka tillståndet för ledigt på en nod, så att den kan rapportera ”AvailableDisk”-egenskapen på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. I health store behandlas rapporterna som separata hälsa händelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen fram hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden är\: 'Ogiltig', 'Ok', 'Varning', 'Fel', 'Okänt'. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --käll-id [krävs] | Källnamn som identifierar komponenten klient-watchdog-systemet som genererats fram hälsoinformation. |
| – Beskrivning | Beskrivning av informationen om hälsa. <br><br> Den representerar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4096 tecken. Den angivna strängen är längre, trunkeras den automatiskt. När trunkerad, de sista tecknen i beskrivningen innehåller en markör ”[Truncated]” och totala strängens storlek är 4096 tecken. Förekomst av markören som anger att användare som trunkering inträffade. Observera att när trunkerad, beskrivningen har mindre än 4096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till Service Fabric-gateway som vidarebefordrar till health store. Om Immediate anges till true, rapporten skickas direkt från http-Gateway i health store, oavsett fabric-klientinställningarna som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas rapporten skickas fortfarande, till exempel om HTTP-Gateway är stängt eller meddelandet inte nå gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningar hälsa från HTTP-Gateway. Det är därför batchar enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom det tillåter hälsa klienten att optimera hälsotillståndsrapportering meddelanden till health store, samt för bearbetning. Som standard skickas rapporter inte omedelbart. |
| – ta bort när-har upphört | Värde som anger om rapporten tas bort från health store när den upphör. <br><br> Om värdet är true, rapporten har tagits bort från health store när den upphör. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). Det här sättet är personen har problem (t.ex. deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
| ---sekvensnummer | Sekvensnumret för den här hälsorapport som en numerisk sträng. <br><br> Sekvensnummer rapporten används av health store för att identifiera inaktuella rapporter. Om inget anges genereras ett sekvensnummer automatiskt av klientens hälsa när en rapport har lagts till. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |
| --TTL-värde | Den tid som den här hälsorapport är giltigt. Det här fältet formatet ISO8601 för att ange varaktigheten. <br><br> När klienterna rapporterar med jämna mellanrum, skickar de rapporter med högre frekvens än livslängd. De kan ange time to live till oändliga om klienter rapport i övergång. När time to live-upphör att gälla hälsohändelse som innehåller information om hälsa är antingen bort från health store om RemoveWhenExpired är true och utvärderas vid fel, om RemoveWhenExpired false. Om inte tidpunkten på live oändligt värde som standard. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-restart"></a>sfctl partition omstart
Detta API startar om vissa eller alla repliker eller instanser av den angivna partitionen.

Detta API är användbart för testning av redundans. Om används för att rikta en tillståndslösa tjänsten partition, vara RestartPartitionMode AllReplicasOrInstances. Anropa API: et för GetPartitionRestartProgress som använder samma åtgärds-ID för att hämta förloppet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --omstart partition-läge [krävs] | Beskriv vilka partitioner att starta om. |
| --tjänst-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-restart-status"></a>sfctl partition omstart-status
Hämtar en PartitionRestart åtgärdens igång med StartPartitionRestart förlopp.

Hämtar status för en PartitionRestart igång med StartPartitionRestart med hjälp av det tillhandahållna åtgärds-ID.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --tjänst-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-partition-svc-name"></a>sfctl svc-partitionsnamnet
Hämtar namnet på Service Fabric-tjänst för en partition.

Hämtar namnet på tjänsten för den angivna partitionen. Ett 404-fel returneras om partitions-ID inte finns i klustret.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identitet för partitionen. |
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
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
