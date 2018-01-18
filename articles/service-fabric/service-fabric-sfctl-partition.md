---
title: Azure Service Fabric CLI - sfctl partition | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl partition-kommandon.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: 9d709a0ec2b7de985ac08fe9ee2935848e7a371c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-partition"></a>sfctl partition
Fråga efter och hantera partitioner för alla tjänster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
|    förlust av data      | Detta API startar förlust av data för den angivna partitionen.|
|    data-loss-status  | Hämtar partition data går förlorade åtgärdens igång med API: et StartDataLoss förlopp.|
|    hälsa         | Hämtar hälsotillståndet för den angivna Service Fabric-partitionen.|
|    info           | Hämtar information om en Service Fabric-partition.|
|    lista           | Hämtar listan över partitioner i en Service Fabric-tjänst.|
|    läsa in           | Hämtar belastningen på den angivna Service Fabric-partitionen.|
|    Läs in återställning     | Återställer den aktuella belastningen för Service Fabric-partitionen.|
|    quorum-loss    | Startar förlorar kvorum för en given tillståndskänslig service-partition.|
|    quorum-loss-status| Hämtar status för ett kvorum förlust åtgärden på en partition som startats med hjälp av StartQuorumLoss-API.|
|    Återställ        | Anger att den ska försöka att återställa en specifik partition, som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret.|
|    recover-all    | Anger att den ska försöka att återställa några tjänster (inklusive systemtjänster) som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret.|
|    report-health  | Skickar en hälsorapport i Service Fabric-partitionen.|
|    Starta om        | Detta API startar om vissa eller alla repliker eller instanser av den angivna partitionen.|
|    Starta om-status | Hämtar en PartitionRestart åtgärdens igång med StartPartitionRestart förlopp.|
|    svc-name       | Hämtar namnet på Service Fabric-tjänst för en partition.|


## <a name="sfctl-partition-health"></a>sfctl partition hälsa
Hämtar hälsotillståndet för den angivna Service Fabric-partitionen.

Hämtar information om hälsa för den angivna partitionen. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillstånd händelser som rapporterats för tjänsten baserat på hälsotillståndet.
Använd ReplicasHealthStateFilter för att filtrera objektsamlingen ReplicaHealthState på partitionen. Om du anger en partition som inte finns i health store returnerar denna cmdlet ett fel. .

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs]| Identitet för partitionen.|
| --events-health-state-filter  | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd.                Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4). -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.                -Alla - Filter som matchar indata med ett värde för HealthState.                Värdet är 65535.|
|--Utelämna hälsostatistik   | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel.|
| --replicas-health-state-filter| Tillåter filtrering objektsamlingen ReplicaHealthState på partitionen. Värdet kan hämtas från medlemmar eller binär åtgärder på medlemmar i HealthStateFilter. Repliker som matchar filtret returneras. Alla repliker används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4). De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
| --timeout -t               | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                    | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                  | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.                Standard: json.|
| --fråga                    | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/. |
| -verbose                  | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-partition-info"></a>sfctl partition info
Hämtar information om en Service Fabric-partition.

Partitioner slutpunkten returnerar information om den angivna partitionen. Svaret innehåller partitions-ID, information om partitionering schemat, nycklar som stöds av partitionen, status, hälsotillstånd och annan information om partitionen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs]| Identitet för partitionen.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-partition-list"></a>sfctl Partitionslista
Hämtar listan över partitioner i en Service Fabric-tjänst.

Hämtar listan över partitioner i en Service Fabric-tjänst. S partitions-ID, partitionering schemat information, nycklar som stöds av partitionen, status, hälsotillstånd och annan information om partitionen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs]| Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Till exempel om tjänstens namn är ”fabric://myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp ~ app1 ~ svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner.|
| --continuation-token| Parametern fortsättning token för att hämta nästa uppsättning resultat.         En fortsättningstoken med ett icke-tom värde ingår i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL.|
| --timeout -t        | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug             | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h           | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o         | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga             | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose           | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-partition-load"></a>sfctl partitionsbelastning
Hämtar belastningen på den angivna Service Fabric-partitionen.

Returnerar information om den angivna partitionen. Svaret innehåller en lista över belastningen information. Varje information innehåller mått namn, värde och rapporterade senast i UTC.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs]| Identitet för partitionen.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-partition-recover"></a>återställa sfctl partition
Anger att den ska försöka att återställa en specifik partition som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret.

Anger att den ska försöka att återställa en specifik partition som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret. Den här åtgärden bör bara utföras om det är känt att replikerna är nere inte kan återställas. Felaktig användning av denna API kan orsaka förlust av data.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs]| Identitet för partitionen.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-partition-restart"></a>sfctl partition omstart
Detta API startar om vissa eller alla repliker eller instanser av den angivna partitionen.

Detta API är användbart för testning av redundans. Om används för att rikta en tillståndslösa tjänsten partition, vara RestartPartitionMode AllReplicasOrInstances. Anropa API: et för GetPartitionRestartProgress som använder samma åtgärds-ID för att hämta förloppet. .

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs]| Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress.|
| --partitions-id [krävs]| Identitet för partitionen.|
| --omstart partition-läge [krävs]| -Ogiltig - reserverade.  Överför inte till API-gränssnitt. -AllReplicasOrInstances - alla repliker eller instanser i partitionen har startats om på samma gång. -OnlyActiveSecondaries - endast sekundärt repliker har startats om. .|
| --tjänst-id [krävs]| Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Till exempel om tjänstens namn är ”fabric://myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp ~ app1 ~ svc1” i 6.0 + och ”myapp/app1/svc1” i föregående v ersions.|
| --timeout -t                    | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                         | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                       | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                     | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.                     Standard: json.|
| --fråga                         | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose                       | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
