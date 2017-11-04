---
title: Azure Service Fabric CLI - sfctl service | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl service-kommandon.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 66649bb6ae317eb227dcdf45aa084905967c117f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-service"></a>sfctl service
Skapa, ta bort och hantera service, tjänsttyp och servicepaket.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
|    appens namn-       | Hämtar namnet på Service Fabric-program för en tjänst.|
|    koden paketlistan | Hämtar listan över kod paket distribueras på ett Service Fabric-nod.|
|    skapa         | Skapar den angivna Service Fabric-tjänsten från beskrivningen.|
|    ta bort         | Tar bort en befintlig Service Fabric-tjänst.|
|    distribuerat typ  | Hämtar information om en angiven tjänsttyp av program som distribuerats på en nod i ett Service Fabric-kluster.|
|    distribuerat-typ-lista| Hämtar en lista som innehåller information om tjänsttyper från program som har distribuerats på en nod i ett Service Fabric-kluster.|
|    description    | Hämtar en beskrivning av en befintlig Service Fabric-tjänst.|
|    Hälsotillstånd         | Hämtar hälsotillståndet för den angivna Service Fabric-tjänsten.|
|    Info           | Hämtar information om specifik tjänst som hör till ett Service Fabric-program.|
|    lista           | Hämtar information om alla tjänster som hör till det program som anges av det program-ID.|
|    Manifestet       | Hämtar manifestet som beskriver en tjänsttyp.|
|    distribuera paketet | Hämtar paket som är associerade med angivna tjänstmanifestet till det bild-cacheminnet på angivna noden.|
|    paketet hälsa | Hämtar information om hälsa i ett abonnemang för ett visst program som distribueras för Service Fabric-noden och program.|
|    paket-info   | Hämtar listan över servicepaket distribueras på ett Service Fabric-noden som matchar exakt det angivna namnet.|
|    paket-lista   | Hämtar listan över servicepaket som har distribuerats på en Service Fabric-nod.|
|    Återställ        | Anger att den ska försöka återställa den angivna tjänsten är för närvarande fastnat i kvorumförlust till Service Fabric-klustret.|
|    rapporten hälsa  | Skickar en hälsorapport för Service Fabric-tjänsten.|
|    Lös        | Lösa en Service Fabric-partition.|
|    Ange-lista      | Hämtar en lista som innehåller information om tjänsttyper som stöds av en etablerad programtyp i ett Service Fabric-kluster.|
|    Uppdatering         | Uppdaterar den angivna tjänsten med hjälp av den angivna beskrivningen.|


## <a name="sfctl-service-create"></a>sfctl skapa
Skapar den angivna Service Fabric-tjänsten från beskrivningen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --app-id [krävs]| Identiteten för det överordnade programmet. Detta är vanligtvis fullständig ID för program utan de ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ' ~' tecken. Till exempel om programmet heter 'fabric://myapp/app1' programidentiteten skulle vara ' myapp ~ app1' i 6.0 + och ' myapp/app1 ”i tidigare versioner.|
| --name [krävs]| Namnet på tjänsten. Detta bör vara en underordnad i program-ID.           Fullständiga namn inklusive den `fabric:` URI. Till exempel tjänsten `fabric:/A/B` är underordnat program `fabric:/A`.|
| --tjänsttypen [krävs]| Namn på tjänst.|
| --activation-läge     | Aktivering läge för tjänstepaketet.|
| --begränsningar         | Placeringen som en sträng. Placeringen är booleskt uttryck i Egenskaper för en nod och tillåter för att begränsa en tjänst till vissa noder baserat på kraven på tjänster. Till exempel för att placera en tjänst på noder där NodeType är blå anger du följande ”: NodeColor == blå”.|
| --korrelerade-tjänst  | Namnet på Måltjänsten för korrelation med.|
| --korrelation         | Korrelera tjänsten med en befintlig tjänst med hjälp av en tillhörighet för justering.|
| --dns-namn            | DNS-namnet på tjänsten som ska skapas. Tjänsten DNS för Service Fabric-system måste aktiveras för den här inställningen.|
| --antal instanser      | Instansantalet. Detta gäller för tillståndslösa tjänster.|
| --int-schema          | Anger att tjänsten ska partitioneras enhetligt över en mängd osignerat heltal.|
| --int-schema-antal    | Antalet partitioner i heltal key intervallet (för ett partitionsschema för enhetlig heltal) för att skapa.|
| --int schemat hög     | Slutet av viktiga heltalsintervall om du använder ett partitionsschema för enhetlig heltal.|
| --int och schema-låg      | Början på viktiga heltalsintervall om du använder ett partitionsschema för enhetlig heltal.|
| --belastningen mått        | JSON-kodade listan över mått som används när belastningsutjämning services mellan noder.|
| --min---storlek på replikuppsättningen| Minsta replikuppsättningens storlek som ett tal. Detta gäller endast tillståndskänsliga tjänster.|
| – Flytta kostnad           | Anger flytta kostnaden för tjänsten. Möjliga värden är: 'Noll', lågt', 'Medelhög', 'Högt'.|
| --med namnet schema        | Anger att tjänsten ska ha flera namngivna partitioner.|
| --med namnet-schema-lista   | JSON-kodade namnlista partitionera tjänsten över, om du använder namngivna partitionsschemat.|
| --Nej sparade tillstånd  | Om värdet är true anger detta tjänsten har ingen beständig tillstånd som lagras på den lokala disken eller lagrar bara tillstånd i minnet.|
| --placering principlistan  | JSON-kodade lista över placeringsprinciper för tjänsten och alla associerade domännamn. Principer kan vara en eller flera av: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --kvorum-förlust-vänta    | Maximal varaktighet i sekunder som en partition kan vara i tillståndet förlorar kvorum. Detta gäller endast tillståndskänsliga tjänster.|
| --Vänta-replik-omstart| Varaktighet i sekunder mellan när en replik kraschar och när en replik skapas. Detta gäller endast tillståndskänsliga tjänster.|
| --singleton-schema    | Anger att tjänsten ska ha en enda partition eller vara en icke - partitionerade tjänst.|
| --vänteläge av repliken keep  | Maximal varaktighet i sekunder, för vilka vänteläge bevaras repliker innan den tas bort. Detta gäller endast tillståndskänsliga tjänster.|
| --stateful            | Anger att tjänsten är en tjänst för tillståndskänsliga.|
| --tillståndslös           | Anger att tjänsten är en tillståndslös tjänst.|
| ---replik-set-Målstorlek| Replikuppsättningens Ange storlek som ett tal. Detta gäller endast tillståndskänsliga tjänster.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-service-delete"></a>ta bort sfctl-tjänsten
Tar bort en befintlig Service Fabric-tjänst.

Tar bort en befintlig Service Fabric-tjänst. En tjänst måste skapas innan den kan tas bort. Som standard försöker Service Fabric Stäng tjänsten repliker på ett korrekt sätt och ta bort tjänsten. Men om tjänsten har problem med att stänga repliken, delete-åtgärden kan ta lång tid eller fastnar. Använda flaggan valfria ForceRemove hoppa över korrekt sekvensstängning och ta bort tjänsten tvång.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs]| Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Om exempelvis tjänstnamnet är fabric://myapp/app1/svc1 ”, tjänstidentiteten skulle vara” myapp ~ app1 ~ svc1 ”i 6.0 + och” myapp/app1/svc1 ”i tidigare versioner.|
| --force-ta bort      | Ta bort ett Service Fabric-program eller tjänst tvång utan att gå igenom alla korrekt avslutning. Den här parametern kan användas för att tvång ta bort ett program eller tjänst för vilka borttagning är avbryts på grund av problem i den kod som förhindrar korrekt Stäng av replikerna.|
| --timeout -t        | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning             | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h           | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o         | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga             | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose           | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-service-description"></a>Beskrivning av sfctl
Hämtar en beskrivning av en befintlig Service Fabric-tjänst.

Hämtar en beskrivning av en befintlig Service Fabric-tjänst. En tjänst måste skapas innan dess beskrivning kan hämtas.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs]| Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Till exempel om tjänstens namn är ”fabric://myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp ~ app1 ~ svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner.|
| --timeout -t        | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning             | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h           | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o         | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga             | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose           | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-service-health"></a>sfctl tjänstens hälsa
Hämtar hälsotillståndet för den angivna Service Fabric-tjänsten.

Hämtar information om hälsa för den angivna tjänsten. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillstånd händelser som rapporterats för tjänsten baserat på hälsotillståndet. Använd PartitionsHealthStateFilter att filtrera partitionssamlingen returneras. Om du anger en tjänst som inte finns i health store returnerar denna cmdlet ett fel. .

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs]| Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Till exempel om tjänstens namn är ”fabric://myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp ~ app1 ~ svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner.|
| --händelser-hälsa-tillstånd-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4). -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
|--Utelämna hälsostatistik     | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel.|
| --partitioner-hälsa-tillstånd-filter| Tillåter filtrering av partitioner hälsa tillstånd objekt returneras i resultatet av tjänstens hälsa fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast partitioner som matchar filtret returneras. Alla partitioner för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är ”6” hälsotillståndet för partitioner med HealthState värdet OK (2) och varning (4) returneras. -Standard - standardvärde. Matchar alla HealthState.                  Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
| --timeout -t                 | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning                      | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                    | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                  | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.                  Standard: json.|
| --fråga                      | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose                    | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-service-info"></a>sfctl tjänstinformation
Hämtar information om specifik tjänst som hör till ett Service Fabric-program.

Returnerar information om angiven tjänst som hör till det angivna Service Fabric-programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs]| Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Till exempel om programmet heter ”fabric://myapp/app1” programidentiteten skulle vara ”myapp ~ app1” i 6.0 + och ”myapp/app1” i tidigare versioner.|
| --tjänst-id [krävs]| Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Till exempel om tjänstens namn är ”fabric://myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp ~ app1 ~ svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner.|
| --timeout -t            | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning                 | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h               | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o             | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                 | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose               | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-service-list"></a>sfctl Tjänstlista
Hämtar information om alla tjänster som hör till det program som anges av det program-ID.

Returnerar information om alla tjänster som hör till det program som anges av det program-ID.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs]| Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Till exempel om programmet heter ”fabric://myapp/app1” programidentiteten skulle vara ”myapp ~ app1” i 6.0 + och ”myapp/app1” i tidigare versioner.|
| --fortsättningstoken    | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tom värde ingår i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL.|
| --tjänsten typnamn     | Tjänsten typnamn som används för att filtrera de tjänster att fråga efter.|
| --timeout -t            | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning                 | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h               | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o             | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                 | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose               | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-service-manifest"></a>sfctl tjänstmanifestet
Hämtar manifestet som beskriver en tjänsttyp.

Hämtar manifestet som beskriver en tjänsttyp. Svaret innehåller tjänstmanifestet XML som en sträng.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-typnamn [krävs]| Namnet på programtyp.|
| --program-type-version [krävs]| Versionen av programtypen.|
| ---manifest-tjänstnamn [krävs]| Namnet på ett service manifest som registrerats som en del av en typ av program i ett Service Fabric-kluster.|
| --timeout -t                      | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning                           | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                         | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                       | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.                       Standard: json.|
| --fråga                           | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose                         | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-service-recover"></a>sfctl service Återställ
Anger att den ska försöka återställa den angivna tjänsten är för närvarande fastnat i kvorumförlust till Service Fabric-klustret.

Anger att den ska försöka återställa den angivna tjänsten är för närvarande fastnat i kvorumförlust till Service Fabric-klustret. Den här åtgärden bör bara utföras om repliker som är nere inte kan återställas. Felaktig användning av denna API kan orsaka förlust av data.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs]| Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Om exempelvis tjänstnamnet är fabric://myapp/app1/svc1 ”, tjänstidentiteten skulle vara” myapp ~ app1 ~ svc1 ”i 6.0 + och” myapp/app1/svc1 ”i tidigare versioner.|
| --timeout -t        | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning             | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h           | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o         | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga             | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose           | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-service-resolve"></a>sfctl service Lös
Lösa en Service Fabric-partition.

Lösa en partition för Service Fabric-tjänsten, för att få slutpunkter repliker för tjänsten.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs]| Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Till exempel om tjänstens namn är ”fabric://myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp ~ app1 ~ svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner.|
| --partition nyckeltypen| Nyckeltypen för partitionen. Den här parametern krävs om partitionsschema för tjänsten är Int64Range eller namn. Möjliga värden följande. -Ingen (1) – anger att parametern PartitionKeyValue inte har angetts. Detta är giltig för partitioner med partitioneringsschema som Singleton. Detta är standardvärdet. Värdet är 1. -Int64Range (2) - anger att parametern PartitionKeyValue är en partitionsnyckel int64. Detta är giltig för partitioner med partitioneringsschema som Int64Range. Värdet är 2. -Namn (3) - anger att parametern PartitionKeyValue är ett namn för partitionen. Detta är giltig för partitioner med partitioneringsschema som namn. Värdet är 3.|
| --partition nyckelvärde  | Partitionsnyckeln. Detta krävs om partitionsschema för tjänsten är Int64Range eller namn.|
| --tidigare rsp version | Värdet i fältet Version av svar som tagits emot tidigare. Detta krävs om användaren känner att resultatet var fick tidigare är inaktuella.|
| --timeout -t        | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning             | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h           | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o         | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga             | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose           | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-service-update"></a>sfctl tjänstuppdatering
Uppdaterar den angivna tjänsten med hjälp av den angivna beskrivningen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs]| Måltjänsten att uppdatera. Detta är vanligtvis fullständig ID för tjänsten utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Till exempel om tjänstens namn är 'fabric://myapp/app1/svc1', tjänstidentiteten skulle vara ' myapp ~ app1 ~ svc1' i 6.0 + och ' myapp/app1/svc1' i tidigare versioner.|
| --begränsningar         | Placeringen som en sträng. Placeringen är booleskt uttryck i Egenskaper för en nod och tillåter för att begränsa en tjänst till vissa noder baserat på kraven på tjänster. Till exempel för att placera en tjänst på noder där NodeType är blå anger du följande ”: NodeColor == blå”.|
| --korrelerade-tjänst  | Namnet på Måltjänsten för korrelation med.|
| --korrelation         | Korrelera tjänsten med en befintlig tjänst med hjälp av en tillhörighet för justering.|
| --antal instanser      | Instansantalet. Detta gäller för tillståndslösa tjänster.|
| --belastningen mått        | JSON-kodade listan över mått används när belastningsutjämning mellan noder.|
| --min---storlek på replikuppsättningen| Minsta replikuppsättningens storlek som ett tal. Detta gäller endast tillståndskänsliga tjänster.|
| – Flytta kostnad           | Anger flytta kostnaden för tjänsten. Möjliga värden är: 'Noll', lågt', 'Medelhög', 'Högt'.|
| --placering principlistan  | JSON-kodade lista över placeringsprinciper för tjänsten och alla associerade domännamn. Principer kan vara en eller flera av: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --kvorum-förlust-vänta    | Maximal varaktighet i sekunder som en partition kan vara i tillståndet förlorar kvorum. Detta gäller endast tillståndskänsliga tjänster.|
| --Vänta-replik-omstart| Varaktighet i sekunder mellan när en replik kraschar och när en replik skapas. Detta gäller endast tillståndskänsliga tjänster.|
| --vänteläge av repliken keep  | Maximal varaktighet i sekunder, för vilka vänteläge bevaras repliker innan den tas bort. Detta gäller endast tillståndskänsliga tjänster.|
| --stateful            | Anger Måltjänsten är en tillståndskänslig service.|
| --tillståndslös           | Anger Måltjänsten är en tillståndslös tjänst.|
| ---replik-set-Målstorlek| Replikuppsättningens Ange storlek som ett tal. Detta gäller endast tillståndskänsliga tjänster.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).