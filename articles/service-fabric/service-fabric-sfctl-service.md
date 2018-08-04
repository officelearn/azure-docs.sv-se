---
title: Azure Service Fabric CLI sfctl tjänst | Microsoft Docs
description: Beskriver sfctl service-kommandon för Service Fabric CLI.
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
ms.openlocfilehash: 84c2faaf137e19d78e7e17527feb50baebf8041b
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494582"
---
# <a name="sfctl-service"></a>sfctl service
Skapa, ta bort och hantera tjänsten, tjänsttyper och paket.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| appens namn | Hämtar namnet på Service Fabric-program för en tjänst. |
| Code-package-list | Hämtar listan över kodpaket som distribuerats på en Service Fabric-nod. |
| skapa | Skapar den angivna Service Fabric-tjänsten. |
| delete | Tar bort en befintlig Service Fabric-tjänst. |
| distribuerat typ | Hämtar information om en angiven tjänsttyp av program som distribuerats på en nod i ett Service Fabric-kluster. |
| distribuerat-type-list | Hämtar listan som innehåller information om tjänsttyper från program som har distribuerats på en nod i ett Service Fabric-kluster. |
| beskrivning | Hämtar en beskrivning av en befintlig Service Fabric-tjänst. |
| Hämta behållarloggar | Hämtar behållarloggarna för behållare som distribueras på en Service Fabric-nod. |
| hälsotillstånd | Hämtar hälsotillståndet för den angivna Service Fabric-tjänsten. |
| info | Hämtar information om den tjänst som hör till Service Fabric-program. |
| lista | Hämtar information om alla tjänster som hör till programmet som anges av program-ID. |
| Manifest | Hämtar manifestet som beskriver en typ av tjänst. |
| distribuera paket | Laddar ned paket som är associerade med angivna tjänstmanifestet till image cache på angivna noden. |
| paket-health | Hämtar information om hälsotillståndet i ett abonnemang för ett visst program som distribueras för en Service Fabric-nod och program. |
| package-info | Hämtar listan över paket distribueras på en Service Fabric-nod som matchar exakt det angivna namnet. |
| Package-list | Hämtar listan över paket distribueras på en Service Fabric-nod. |
| återställa | Anger att den ska försöka återställa den angivna tjänsten som för närvarande har fastnat förlorar kvorum till Service Fabric-klustret. |
| rapportera hälsa | Skickar en hälsorapport på Service Fabric-tjänst. |
| lös | Lösa en Service Fabric-partition. |
| typ-list | Hämtar listan som innehåller information om typer av tjänster som stöds av en etablerad programtyp i Service Fabric-kluster. |
| update | Uppdaterar den angivna tjänsten med hjälp av en viss uppdatering beskrivning. |

## <a name="sfctl-service-app-name"></a>sfctl app-tjänstnamn
Hämtar namnet på Service Fabric-program för en tjänst.

Hämtar namnet på programmet för den angivna tjänsten. Ett 404 FABRIC_E_SERVICE_DOES_NOT_EXIST-fel returneras om en tjänst med det angivna service-ID: T inte finns.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-code-package-list"></a>sfctl service code-package-list
Hämtar listan över kodpaket som distribuerats på en Service Fabric-nod.

Hämtar listan över kodpaket som distribuerats på en Service Fabric-nod för det angivna programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --kod paketnamn | Namnet på kodpaketet som anges i tjänstmanifestet registrerad som en del av en typ av program i ett Service Fabric-kluster. |
| --manifest tjänstnamnet | Namnet på ett tjänstmanifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-create"></a>Skapa sfctl-tjänsten
Skapar den angivna Service Fabric-tjänsten.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| [krävs] – app-id | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den '\~' tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1 ', programidentiteten skulle vara” myapp\~app1' i 6.0 + och ”myapp/app1' i tidigare versioner. |
| --name [krävs] | Namnet på tjänsten. Det bör vara en underordnad till program-id. Fullständiga namnet, inklusive den `fabric\:` URI. Till exempel tjänsten `fabric\:/A/B` är underordnad programmet `fabric\:/A`. |
| --tjänsttyp [krävs] | Namnet på tjänsten. |
| --aktivering-läge | Aktivering läge för tjänstpaketet. |
| --begränsningar | Placeringen som en sträng. Placeringsbegränsningar är booleska uttryck på nodegenskaper och tillåter för att begränsa en tjänst till specifika noder baserat på kraven på tjänster. Till exempel för att placera en tjänst på noder där NodeType är blå anger du följande\:”NodeColor == blå”. |
| --korrelerade-tjänst | Namnet på Måltjänsten att korrelera med. |
| --korrelation | Korrelera tjänsten med en befintlig tjänst med hjälp av en mappning för justering. |
| --dns-name | DNS-namnet på tjänsten som ska skapas. Service Fabric DNS system-tjänsten måste aktiveras för den här inställningen. |
| – antal instanser | Instansantalet. Detta gäller för tillståndslösa tjänster. |
| – int-schema | Anger tjänsten bör partitioneras enhetligt över flera olika osignerade heltal. |
| – int-schema-count | Antalet partitioner i viktiga heltalsintervall att skapa, om du använder en enhetlig heltal partitionsschema. |
| – int-schema-hög | Slutet av viktiga heltalsintervall om du använder en enhetlig heltal partitionsschema. |
| – int och schema-låg | Början på viktiga heltalsintervall om du använder en enhetlig heltal partitionsschema. |
| – Läs in mått | JSON-kodad lista över mått som används när Utjämning av nätverksbelastning services mellan noder. |
| --min –--replikuppsättning | Minsta replikuppsättningens storlek som ett tal. Detta gäller för tillståndskänsliga tjänster. |
| – Flytta kostnad | Anger flytta kostnaden för tjänsten. Möjliga värden är\: ”noll”, ”låg”, ”medel”, ”hög”. |
| --med namnet schema | Anger att tjänsten ska ha flera namngivna partitioner. |
| --named-scheme-list | JSON-kodad namnlista att partitionera service mellan dem, om du använder namngivna partitionsschemat. |
| --inga sparade tillstånd | Om värdet är true anger detta tjänsten har inga beständiga tillstånd som lagras på den lokala disken eller lagrar bara tillstånd i minnet. |
| --placering policy list | JSON-kodad lista över placeringsprinciper för tjänsten och alla associerade domännamn. Principer kan vara en eller flera av\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --kvorum-förlust-wait | Tidsgräns i sekunder som en partition får vara i tillståndet hos förlorar kvorum. Detta gäller för tillståndskänsliga tjänster. |
| --replik-omstart-wait | Varaktighet i sekunder mellan när en replik kraschar och när en ny replik skapas. Detta gäller för tillståndskänsliga tjänster. |
| --skalning principer | JSON-kodad lista över skalning principer för den här tjänsten. |
| --singleton-schema | Anger att tjänsten ska ha en partition eller vara en icke-partitionerad tjänst. |
| – fristående av repliken keep | Den maximala varaktigheten i sekunder, för vilka vänteläge bevaras repliker innan den tas bort. Detta gäller för tillståndskänsliga tjänster. |
| --tillståndskänslig | Anger att tjänsten är en tillståndskänslig tjänst. |
| --tillståndslösa | Anger att tjänsten är en tillståndslös tjänst. |
| --mål –--replikuppsättning | Target replikuppsättningens storlek som ett tal. Detta gäller för tillståndskänsliga tjänster. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-delete"></a>sfctl service delete
Tar bort en befintlig Service Fabric-tjänst.

En tjänst måste skapas innan den kan tas bort. Som standard försöker Service Fabric att stänga service repliker på ett korrekt sätt och sedan ta bort tjänsten. Men om tjänsten har problem med stänger repliken utan problem, borttagningen kan ta lång tid eller fastnar. Använder du flaggan valfritt ForceRemove hoppas över korrekt Stäng sekvensen och ta bort tjänsten kernelpaketet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| – force-ta bort | Ta bort en Service Fabric-program eller tjänst kernelpaketet utan att gå igenom de avslutning. Den här parametern kan användas för att kernelpaketet ta bort ett program eller tjänst för vilka borttagning är avbryts på grund av problem i den kod som förhindrar korrekt slutet av repliker. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-deployed-type"></a>sfctl tjänsten distribueras typ
Hämtar information om en angiven tjänsttyp av program som distribuerats på en nod i ett Service Fabric-kluster.

Hämtar listan som innehåller information om en specifik tjänsttyp från program som har distribuerats på en nod i ett Service Fabric-kluster. Svaret innehåller namnet på tjänsttypen, dess registreringsstatus, kodpaketet som registrerade den och aktivering ID för tjänstpaketet. Varje post representerar en aktivering av en tjänsttyp differentierade av aktivering-ID.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --service-typnamn [krävs] | Anger namnet på en tjänsttyp i Service Fabric. |
| --manifest tjänstnamnet | Namnet på tjänstmanifestet att filtrera listan med information om distribuerade tjänsten. Om svaret endast innehåller information om typer av tjänster som definieras i den här tjänstmanifestet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl tjänsten distribueras-type-list
Hämtar listan som innehåller information om tjänsttyper från program som har distribuerats på en nod i ett Service Fabric-kluster.

Hämtar listan som innehåller information om tjänsttyper från program som har distribuerats på en nod i ett Service Fabric-kluster. Svaret innehåller namnet på tjänsttypen, dess registreringsstatus, kodpaketet som registrerade den och aktivering ID för tjänstpaketet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --manifest tjänstnamnet | Namnet på tjänstmanifestet att filtrera listan med information om distribuerade tjänsten. Om svaret endast innehåller information om typer av tjänster som definieras i den här tjänstmanifestet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-description"></a>Beskrivning av sfctl
Hämtar en beskrivning av en befintlig Service Fabric-tjänst.

Hämtar en beskrivning av en befintlig Service Fabric-tjänst. En tjänst måste skapas innan dess beskrivning kan hämtas.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-get-container-logs"></a>sfctl get-container-tjänstloggar
Hämtar behållarloggarna för behållare som distribueras på en Service Fabric-nod.

Hämtar behållarloggarna för behållare som har distribuerats på en Service Fabric-nod för det givna code-paketet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --code-package-name [krävs] | Namnet på kodpaketet som anges i tjänstmanifestet registrerad som en del av en typ av program i ett Service Fabric-kluster. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --manifest-tjänstnamnet [krävs] | Namnet på ett tjänstmanifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --tidigare | Anger om du vill hämta behållarloggar från avslutades/dead behållare för kod paket-instansen. |
| --pilslut | Antal rader som ska visas från slutet av loggarna. Standardvärdet är 100. ”alla” för att visa de fullständiga loggarna. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-health"></a>sfctl tjänstehälsa
Hämtar hälsotillståndet för den angivna Service Fabric-tjänsten.

Hämtar hälsoinformation om den angivna tjänsten. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillståndshändelser för tjänsten baserat på hälsotillståndet rapporteras. Använd PartitionsHealthStateFilter att filtrera partitionssamlingen returneras. Om du anger en tjänst som inte finns i health store, returneras ett fel i den här begäran.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --Exkludera hälsostatistik | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel. |
| --partitions-health-state-filter | Tillåter filtrering av partitioner hälsotillstånd tillstånd objekt returneras i resultatet för service health-frågan baserat på deras hälsotillstånd. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast partitioner som matchar filtret returneras. Alla partitioner som används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 returneras sedan hälsotillståndet för partitioner med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-info"></a>sfctl serviceinfo
Hämtar information om den tjänst som hör till Service Fabric-program.

Returnerar information om den angivna tjänsten som hör till det angivna Service Fabric-programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-list"></a>sfctl Tjänstlista
Hämtar information om alla tjänster som hör till programmet som anges av program-ID.

Returnerar information om alla tjänster som hör till programmet som anges av program-ID.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --fortsättningstoken | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| --service typnamn | Typnamn service används för att filtrera tjänster att fråga efter. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-manifest"></a>sfctl tjänstmanifestet
Hämtar manifestet som beskriver en typ av tjänst.

Hämtar manifestet som beskriver en typ av tjänst. Svaret innehåller tjänstmanifestet XML som en sträng.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-typnamn [krävs] | Namnet på vilken typ av program. |
| –--version av programtyp [krävs] | Versionen av programtypen. |
| --manifest-tjänstnamnet [krävs] | Namnet på ett tjänstmanifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-package-deploy"></a>sfctl service package-distribuera
Laddar ned paket som är associerade med angivna tjänstmanifestet till image cache på angivna noden.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --app-typnamn [krävs] | Namnet på programmanifestet för motsvarande begärda tjänstmanifestet. |
| --app-typ-version [krävs] | Versionen av manifestet för motsvarande begärda tjänstmanifestet. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --manifest-tjänstnamnet [krävs] | Namnet på tjänstmanifestet som är associerade med de paket som ska laddas ned. |
| --resurs-policy | JSON-kodad lista över delning principer. Varje delningsapplikationen principelement består av en ”namn” och ”omfång”. Namnet som motsvarar namnet på det kod, konfiguration eller paket som ska delas. Omfånget kan vara antingen ”ingen”, ”alla”, ”Code”, ”Config” eller ”Data”. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-package-health"></a>sfctl service package-health
Hämtar information om hälsotillståndet i ett abonnemang för ett visst program som distribueras för en Service Fabric-nod och program.

Hämtar information om hälsotillståndet i ett abonnemang för ett visst program som distribuerats på en Service Fabric-nod. Använd EventsHealthStateFilter att du kan också filtrera för objektsamlingen HealthEvent distribuerat tjänstpaket som baseras på hälsotillståndet rapporteras.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --service-package-name [krävs] | Namnet på service-paketet. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-package-info"></a>sfctl service package-info
Hämtar listan över paket distribueras på en Service Fabric-nod som matchar exakt det angivna namnet.

Returnerar information om tjänstpaket som distribuerats på en Service Fabric-nod för det angivna programmet. De här resultaten anges av servicepaket vars namn matchar exakt namnet på tjänsten paketet som parameter.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --service-package-name [krävs] | Namnet på service-paketet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-package-list"></a>sfctl service package-list
Hämtar listan över paket distribueras på en Service Fabric-nod.

Returnerar information om tjänstpaket som distribuerats på en Service Fabric-nod för det angivna programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-recover"></a>sfctl service Återställ
Anger att den ska försöka återställa den angivna tjänsten som för närvarande har fastnat förlorar kvorum till Service Fabric-klustret.

Anger att den ska försöka återställa den angivna tjänsten som för närvarande har fastnat förlorar kvorum till Service Fabric-klustret. Den här åtgärden bör endast utföras om det är känt att replikerna är nere inte kan återställas. Felaktig användning av detta API kan orsaka förlust av data.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-report-health"></a>sfctl service report-health
Skickar en hälsorapport på Service Fabric-tjänst.

Rapporterar hälsotillståndet för den angivna Service Fabric-tjänsten. Rapporten måste innehålla information om orsaken hälsorapport och egenskapen som har rapporterats. Rapporten skickas till en Service Fabric gateway-tjänsten, som vidarebefordrar till health store. Rapporten kan accepteras av gatewayen, men avvisats av health store efter extra valideringen. Health store kan till exempel avvisa rapporten på grund av en ogiltig parameter, t.ex. ett sekvensnummer som är inaktuella. Kontrollera att rapporten ska visas i hälsohändelser i tjänsten för att se om rapporten har tillämpats i health store.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --health-egenskapen [krävs] | Egenskapen för hälsoinformation. <br><br> En entitet kan ha rapporter om hälsotillstånd för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport flexibiliteten att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan till exempel övervaka status för tillgängliga disken på en nod, så att den kan rapportera ”AvailableDisk”-egenskap på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. Dessa rapporter behandlas i health store, som separata health-händelser för den angivna noden. Tillsammans med målentiteten identifierar egenskapen hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden omfattar\: ”ogiltig”, ”Ok”, ”varning”, ”fel”, ”okänd”. |
| --service-id [krävs] | Identiteten för tjänsten. <br><br> Detta är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den '\~' tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1', tjänstidentiteten skulle vara” myapp\~app1\~svc1' i 6.0 + och ”myapp/app1/svc1' i tidigare versioner. |
| --käll-id [krävs] | Källnamn som identifierar/klientsystemet/watchdog-komponenten som genererat hälsoinformation. |
| – Beskrivning | Beskrivning av hälsoinformation. <br><br> Den motsvarar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4 096 tecken. Om strängen inte trunkeras den automatiskt. När trunkeras, de sista tecknen i beskrivningen innehåller en markör ”[trunkerat]” och totala storleken på målsträngen är 4 096 tecken. Förekomst av markören anger för användare att trunkering inträffade. Observera att när trunkeras, beskrivningen har mindre än 4 096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till en Service Fabric gateway programmet, som vidarebefordrar till health store. Om Immediate anges till SANT, skickas rapporten direkt från HTTP-Gateway till health store, oavsett inställningarna i fabric-klient som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas skicka rapporten fortfarande, till exempel om HTTP-Gateway är stängd eller meddelandet når inte gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningarna hälsotillstånd från HTTP-Gateway. Det kan därför batchhanteras enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom den tillåter hälsotillstånd klienten att optimera reporting meddelanden hälsoarkivet, samt för bearbetning av hälsotillstånd. Som standard skickas rapporter inte omedelbart. |
| – ta bort när-har upphört | Värde som anger om rapporten tas bort från health store när den upphör att gälla. <br><br> Om värdet är true, rapporten tas bort från health store när den upphör att gälla. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). På så sätt kan är personen har problem (t.ex. deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
| ---sekvensnummer | Sekvensnumret för den här hälsorapport som en numerisk sträng. <br><br> Sekvensnumret rapporten används av health store för att identifiera inaktuella rapporter. Om inte anges genereras ett sekvensnummer automatiskt av hälsotillstånd klienten när du lägger till en rapport. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --ttl | Den tid som den här hälsorapport är giltig. Det här fältet använder ISO8601-format för att ange varaktigheten. <br><br> När klienterna rapporterar med jämna mellanrum, bör de skicka rapporter med frekvens som är högre än TTL-värde. Om klienterna rapporterar på övergång, kan de ange time to live till obegränsad. När TTL-värde upphör att gälla hälsohändelsen som innehåller hälsoinformation är antingen tas bort från health store om RemoveWhenExpired är true och utvärderas vid fel, om RemoveWhenExpired false. Om inte tidpunkten TTL-värde standardvärdet är oändligt värde. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Lösa en Service Fabric-partition.

Lösa en partition med Service Fabric-tjänsten för att få slutpunkter av tjänstens repliker.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --service-id [krävs] | Identiteten för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --partition-key-type | Nyckeltypen för partitionen. Den här parametern krävs om partitionsschema för tjänsten är Int64Range eller namn. Möjliga värden följande. -Ingen (1) – anger att parametern PartitionKeyValue inte har angetts. Det här är giltig för partitioner med partitioneringsschema som Singleton. Detta är standardvärdet. Värdet är 1. -Int64Range (2) – anger att parametern PartitionKeyValue är en int64 partitionsnyckel. Det här är giltig för partitioner med partitioneringsschema som Int64Range. Värdet är 2. -Namn (3) – anger att parametern PartitionKeyValue är ett namn på partitionen. Det här är giltig för partitioner med partitioneringsschema som namn. Värdet är 3. |
| --partition-key-value | Partitionsnyckel. Detta krävs om partitionsschema för tjänsten är Int64Range eller namn. |
| --previous-rsp-version | Värdet i fältet Version av ett svar som mottogs tidigare. Detta krävs om användaren känner att det resultat som har hämtat tidigare är inaktuella. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-type-list"></a>sfctl service typ-list
Hämtar listan som innehåller information om typer av tjänster som stöds av en etablerad programtyp i Service Fabric-kluster.

Hämtar listan som innehåller information om typer av tjänster som stöds av en etablerad programtyp i Service Fabric-kluster. Den angivna typen måste finnas. I annat fall returneras ett 404-status.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – program-typnamn [krävs] | Namnet på vilken typ av program. |
| –--version av programtyp [krävs] | Versionen av programtypen. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global argument

|Argumentet|Beskrivning|
| --- | --- |
| --Felsöka | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-service-update"></a>sfctl tjänstuppdatering
Uppdaterar den angivna tjänsten med hjälp av en viss uppdatering beskrivning.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --service-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1', tjänstidentiteten skulle vara” myapp\~app1\~svc1' i 6.0 + och ”myapp/app1/svc1' i tidigare versioner. |
| --begränsningar | Placeringen som en sträng. Placeringsbegränsningar är booleska uttryck på nodegenskaper och tillåter för att begränsa en tjänst till specifika noder baserat på kraven på tjänster. Till exempel för att placera en tjänst på noder där NodeType är blå anger du följande\: ”NodeColor == blå”. |
| --korrelerade-tjänst | Namnet på Måltjänsten att korrelera med. |
| --korrelation | Korrelera tjänsten med en befintlig tjänst med hjälp av en mappning för justering. |
| – antal instanser | Instansantalet. Detta gäller för tillståndslösa tjänster. |
| – Läs in mått | JSON-kodad lista över mått som används när belastningsutjämning mellan noder. |
| --min –--replikuppsättning | Minsta replikuppsättningens storlek som ett tal. Detta gäller för tillståndskänsliga tjänster. |
| – Flytta kostnad | Anger flytta kostnaden för tjänsten. Möjliga värden är\: ”noll”, ”låg”, ”medel”, ”hög”. |
| --placering policy list | JSON-kodad lista över placeringsprinciper för tjänsten och alla associerade domännamn. Principer kan vara en eller flera av\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --kvorum-förlust-wait | Tidsgräns i sekunder som en partition får vara i tillståndet hos förlorar kvorum. Detta gäller för tillståndskänsliga tjänster. |
| --replik-omstart-wait | Varaktighet i sekunder mellan när en replik kraschar och när en ny replik skapas. Detta gäller för tillståndskänsliga tjänster. |
| --skalning principer | JSON-kodad lista över skalning principer för den här tjänsten. |
| – fristående av repliken keep | Den maximala varaktigheten i sekunder, för vilka vänteläge bevaras repliker innan den tas bort. Detta gäller för tillståndskänsliga tjänster. |
| --tillståndskänslig | Anger Måltjänsten är en tillståndskänslig tjänst. |
| --tillståndslösa | Anger Måltjänsten är en tillståndslös tjänst. |
| --mål –--replikuppsättning | Target replikuppsättningens storlek som ett tal. Detta gäller för tillståndskänsliga tjänster. |
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
