---
title: Azure Service Fabric CLI - sfctl service | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl service-kommandon.
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
ms.openlocfilehash: e027bb7f61d19fc274f7eddd8f28e9e6dac099ce
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763552"
---
# <a name="sfctl-service"></a>sfctl service
Skapa, ta bort och hantera service, tjänsttyp och servicepaket.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| appens namn- | Hämtar namnet på Service Fabric-program för en tjänst. |
| koden paketlistan | Hämtar listan över kod paket distribueras på ett Service Fabric-nod. |
| skapa | Skapar den angivna Service Fabric-tjänsten. |
| radera | Tar bort en befintlig Service Fabric-tjänst. |
| distribuerat typ | Hämtar information om en angiven tjänsttyp av program som distribuerats på en nod i ett Service Fabric-kluster. |
| distribuerat-typ-lista | Hämtar en lista som innehåller information om tjänsttyper från program som har distribuerats på en nod i ett Service Fabric-kluster. |
| description | Hämtar en beskrivning av en befintlig Service Fabric-tjänst. |
| Get-behållaren-loggar | Hämtar behållaren loggar för behållaren distribueras på ett Service Fabric-nod. |
| hälsotillstånd | Hämtar hälsotillståndet för den angivna Service Fabric-tjänsten. |
| info | Hämtar information om specifik tjänst som hör till Service Fabric-program. |
| lista | Hämtar information om alla tjänster som hör till det program som anges av det program-ID. |
| Manifestet | Hämtar manifestet som beskriver en tjänsttyp. |
| distribuera paketet | Hämtar paket som är associerade med angivna tjänstmanifestet till det bild-cacheminnet på angivna noden. |
| paketet hälsa | Hämtar information om hälsa i ett abonnemang för ett visst program som distribueras för Service Fabric-noden och program. |
| package-info | Hämtar listan över servicepaket distribueras på ett Service Fabric-noden som matchar exakt det angivna namnet. |
| paket-lista | Hämtar listan över servicepaket som har distribuerats på en Service Fabric-nod. |
| Återställ | Anger att den ska försöka återställa den angivna tjänsten som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret. |
| rapporten hälsa | Skickar en hälsorapport för Service Fabric-tjänsten. |
| lös | Lösa en Service Fabric-partition. |
| Ange-lista | Hämtar en lista som innehåller information om tjänsttyper som stöds av en etablerad programtyp i ett Service Fabric-kluster. |
| Uppdatering | Uppdaterar den angivna tjänsten med hjälp av den angivna beskrivningen. |

## <a name="sfctl-service-app-name"></a>sfctl app-tjänstnamn
Hämtar namnet på Service Fabric-program för en tjänst.

Hämtar namnet på programmet för den angivna tjänsten. Ett 404 FABRIC_E_SERVICE_DOES_NOT_EXIST-fel returneras om en tjänst med det tillhandahållna service-ID: T inte finns.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl service code-paketet-lista
Hämtar listan över kod paket distribueras på ett Service Fabric-nod.

Hämtar listan över kod paket distribueras på ett Service Fabric-noden för det angivna programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --nodnamn [krävs] | Namnet på noden. |
| --koden paketnamn | Namnet på kodpaketet som angetts i service manifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --Manifestet tjänstnamn | Namnet på ett service manifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-create"></a>sfctl skapa
Skapar den angivna Service Fabric-tjänsten.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --app-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den '\~' tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ' myapp\~app1' i 6.0 + och ' myapp/app1 ”i tidigare versioner. |
| --name [krävs] | Namnet på tjänsten. Detta bör vara en underordnad i program-ID. Fullständiga namn inklusive den `fabric\:` URI. Till exempel tjänsten `fabric\:/A/B` är underordnat program `fabric\:/A`. |
| --tjänsttypen [krävs] | Namn på tjänst. |
| --activation-läge | Aktivering läge för tjänstepaketet. |
| --begränsningar | Placeringen som en sträng. Placeringen är booleskt uttryck i Egenskaper för en nod och tillåter för att begränsa en tjänst till vissa noder baserat på kraven på tjänster. Till exempel för att placera en tjänst på noder där NodeType är blå anger du följande\:”NodeColor == blå”. |
| --korrelerade-tjänst | Namnet på Måltjänsten för korrelation med. |
| --korrelation | Korrelera tjänsten med en befintlig tjänst med hjälp av en tillhörighet för justering. |
| --dns-name | DNS-namnet på tjänsten som ska skapas. Tjänsten DNS för Service Fabric-system måste aktiveras för den här inställningen. |
| --antal instanser | Instansantalet. Detta gäller för tillståndslösa tjänster. |
| --int-schema | Anger att tjänsten ska partitioneras enhetligt över en mängd osignerat heltal. |
| --int-schema-antal | Antalet partitioner i viktiga heltalsintervall att skapa om du använder ett partitionsschema för enhetlig heltal. |
| --int schemat hög | Slutet av viktiga heltalsintervall om du använder ett partitionsschema för enhetlig heltal. |
| --int och schema-låg | Början på viktiga heltalsintervall om du använder ett partitionsschema för enhetlig heltal. |
| --belastningen mått | JSON-kodade listan över mått som används när belastningsutjämning services mellan noder. |
| --min---storlek på replikuppsättningen | Minsta replikuppsättningens storlek som ett tal. Detta gäller endast tillståndskänsliga tjänster. |
| – Flytta kostnad | Anger flytta kostnaden för tjänsten. Möjliga värden är\: 'Noll', lågt', 'Medelhög', 'Högt'. |
| --med namnet schema | Anger att tjänsten ska ha flera namngivna partitioner. |
| --named-scheme-list | JSON-kodade namnlista partitionera tjänsten över, om du använder namngivna partitionsschemat. |
| --Nej sparade tillstånd | Om värdet är true anger detta tjänsten har ingen beständig tillstånd som lagras på den lokala disken eller lagrar bara tillstånd i minnet. |
| --placering principlistan | JSON-kodade lista över placeringsprinciper för tjänsten och alla associerade domännamn. Principer kan vara en eller flera av\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --kvorum-förlust-vänta | Maximal varaktighet i sekunder som en partition kan vara i tillståndet förlorar kvorum. Detta gäller endast tillståndskänsliga tjänster. |
| --Vänta-replik-omstart | Varaktighet i sekunder mellan när en replik kraschar och när en replik skapas. Detta gäller endast tillståndskänsliga tjänster. |
| --skalning principer | JSON-kodade lista över skalning principer för den här tjänsten. |
| --singleton-schema | Anger att tjänsten ska ha en enda partition eller vara partitionerade-tjänst. |
| --vänteläge av repliken keep | Maximal varaktighet i sekunder, för vilka vänteläge bevaras repliker innan den tas bort. Detta gäller endast tillståndskänsliga tjänster. |
| --stateful | Anger att tjänsten är en tjänst för tillståndskänsliga. |
| --tillståndslös | Anger att tjänsten är en tillståndslös tjänst. |
| ---replik-set-Målstorlek | Replikuppsättningens Ange storlek som ett tal. Detta gäller endast tillståndskänsliga tjänster. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-delete"></a>ta bort sfctl-tjänsten
Tar bort en befintlig Service Fabric-tjänst.

En tjänst måste skapas innan den kan tas bort. Som standard görs Service Fabric försök att stänga service repliker på ett korrekt sätt och ta bort tjänsten. Dock om tjänsten har problem med att stänga repliken, delete-åtgärden kan ta lång tid eller fastnar. Använda flaggan valfria ForceRemove hoppa över korrekt sekvensstängning och ta bort tjänsten tvång.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --force-ta bort | Ta bort ett Service Fabric-program eller tjänst tvång utan att gå igenom alla korrekt avslutning. Den här parametern kan användas för att tvång ta bort ett program eller tjänst för vilka borttagning är avbryts på grund av problem i den kod som förhindrar korrekt Stäng av replikerna. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-deployed-type"></a>sfctl tjänsten distribueras typ
Hämtar information om en angiven tjänsttyp av program som distribuerats på en nod i ett Service Fabric-kluster.

Hämtar en lista som innehåller information om en viss typ av från program som har distribuerats på en nod i ett Service Fabric-kluster. Svaret innehåller namnet på tjänsttypen, dess registreringsstatus, kodpaketet som registrerade den och aktivering-ID för tjänsten. Varje post representerar en aktivering av service-typen differentierade med activation-ID.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --nodnamn [krävs] | Namnet på noden. |
| --service-typnamn [krävs] | Anger namnet på en typ av Service Fabric. |
| --Manifestet tjänstnamn | Namnet på tjänstmanifestet att filtrera listan över typinformation för distribuerade tjänsten. Om svaret endast innehåller information om tjänsttyper som definieras i den här service manifest. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl tjänsten distribueras-typ-lista
Hämtar en lista som innehåller information om tjänsttyper från program som har distribuerats på en nod i ett Service Fabric-kluster.

Hämtar en lista som innehåller information om tjänsttyper från program som har distribuerats på en nod i ett Service Fabric-kluster. Svaret innehåller namnet på tjänsttypen, dess registreringsstatus, kodpaketet som registrerade den och aktivering-ID för tjänsten.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --nodnamn [krävs] | Namnet på noden. |
| --Manifestet tjänstnamn | Namnet på tjänstmanifestet att filtrera listan över typinformation för distribuerade tjänsten. Om svaret endast innehåller information om tjänsttyper som definieras i den här service manifest. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-description"></a>Beskrivning av sfctl
Hämtar en beskrivning av en befintlig Service Fabric-tjänst.

Hämtar en beskrivning av en befintlig Service Fabric-tjänst. En tjänst måste skapas innan dess beskrivning kan hämtas.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl get-behållaren-tjänstloggar
Hämtar behållaren loggar för behållaren distribueras på ett Service Fabric-nod.

Hämtar behållaren-loggar för behållaren som distribuerats på en Service Fabric-nod för det angivna kodpaketet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| ---paketet-kodnamnet [krävs] | Namnet på kodpaketet som angetts i service manifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --nodnamn [krävs] | Namnet på noden. |
| ---manifest-tjänstnamn [krävs] | Namnet på ett service manifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --tidigare | Anger om du vill hämta loggar för behållaren från avslutades/dead behållare av koden paketet instansen. |
| --pilslut | Antal rader som ska visas från slutet av loggarna. Standardvärdet är 100. 'alla' om du vill visa fullständig loggarna. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-health"></a>sfctl tjänstens hälsa
Hämtar hälsotillståndet för den angivna Service Fabric-tjänsten.

Hämtar information om hälsa för den angivna tjänsten. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillstånd händelser som rapporterats för tjänsten baserat på hälsotillståndet. Använd PartitionsHealthStateFilter att filtrera partitionssamlingen returneras. Om du anger en tjänst som inte finns i health store, returneras ett fel i den här förfrågan.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --Utelämna hälsostatistik | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel. |
| --partitions-health-state-filter | Tillåter filtrering av partitioner hälsa tillstånd objekt returneras i resultatet av tjänstens hälsa fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast partitioner som matchar filtret returneras. Alla partitioner för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 ska sedan hälsotillståndet för partitioner med HealthState värdet OK (2) och varning (4) returneras.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-info"></a>sfctl tjänstinformation
Hämtar information om specifik tjänst som hör till Service Fabric-program.

Returnerar information om den angivna tjänsten som hör till det angivna Service Fabric-programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
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

## <a name="sfctl-service-list"></a>sfctl Tjänstlista
Hämtar information om alla tjänster som hör till det program som anges av det program-ID.

Returnerar information om alla tjänster som hör till det program som anges av det program-ID.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --fortsättningstoken | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett tomt värde inkluderas i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL. |
| --tjänsten typnamn | Tjänsten typnamn som används för att filtrera de tjänster att fråga efter. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-manifest"></a>sfctl tjänstmanifestet
Hämtar manifestet som beskriver en tjänsttyp.

Hämtar manifestet som beskriver en tjänsttyp. Svaret innehåller tjänstmanifestet XML som en sträng.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-typnamn [krävs] | Namnet på programtyp. |
| --program-type-version [krävs] | Versionen av programtypen. |
| ---manifest-tjänstnamn [krävs] | Namnet på ett service manifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-package-deploy"></a>distribuera av sfctl service-paket
Hämtar paket som är associerade med angivna tjänstmanifestet till det bild-cacheminnet på angivna noden.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --app-typnamn [krävs] | Namnet på programmanifestet för motsvarande begärda tjänstmanifestet. |
| --app-type-version [krävs] | Versionen av application manifest för motsvarande begärda tjänstmanifestet. |
| --nodnamn [krävs] | Namnet på noden. |
| ---manifest-tjänstnamn [krävs] | Namnet på service manifest som är associerade med de paket som ska hämtas. |
| --resurs-princip | JSON-kodade lista över delning av principer. Varje delning principelement består av en 'name' och 'scope. Namnet som motsvarar namnet på paketet koden, konfiguration och data som ska delas. Omfattningen kan vara None, 'All', 'Code', 'Config' eller 'Data ”. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-package-health"></a>sfctl paket-hälsotjänst
Hämtar information om hälsa i ett abonnemang för ett visst program som distribueras för Service Fabric-noden och program.

Hämtar information om hälsa för servicepaket för ett visst program som distribuerats på en Service Fabric-nod. Använd EventsHealthStateFilter att du kan också filtrera för insamling av HealthEvent objekt som rapporterats för distribuerade tjänstpaket som baseras på hälsotillståndet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --nodnamn [krävs] | Namnet på noden. |
| ---paketet-tjänstnamn [krävs] | Namnet på tjänstepaketet. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-package-info"></a>sfctl service Paketinformation
Hämtar listan över servicepaket distribueras på ett Service Fabric-noden som matchar exakt det angivna namnet.

Returnerar information om tjänstpaket distribueras på ett Service Fabric-noden för det angivna programmet. De här resultaten anges av servicepaket vars namn matchar exakt namnet på tjänsten paketet som parameter.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --nodnamn [krävs] | Namnet på noden. |
| ---paketet-tjänstnamn [krävs] | Namnet på tjänstepaketet. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-package-list"></a>sfctl service paketlistan
Hämtar listan över servicepaket som har distribuerats på en Service Fabric-nod.

Returnerar information om tjänstpaket distribueras på ett Service Fabric-noden för det angivna programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --nodnamn [krävs] | Namnet på noden. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-recover"></a>sfctl service Återställ
Anger att den ska försöka återställa den angivna tjänsten som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret.

Anger att den ska försöka återställa den angivna tjänsten som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret. Den här åtgärden bör bara utföras om det är känt att replikerna är nere inte kan återställas. Felaktig användning av denna API kan orsaka förlust av data.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>sfctl rapport-hälsotjänst
Skickar en hälsorapport för Service Fabric-tjänsten.

Rapporterar hälsotillståndet för den angivna Service Fabric-tjänsten. Rapporten innehålla information om källan för hälsorapport och egenskapen som har rapporterats. Rapporten skickas till Service Fabric-gateway-tjänsten som vidarebefordrar till health store. Rapporten kan accepteras av gatewayen men avvisas av health store efter extra validering. Health store kan avvisa rapporten på grund av en ogiltig parameter som ett inaktuella sekvensnummer. Kontrollera att rapporten ska visas i händelser som hälsa av tjänsten för att se om rapporten har tillämpats i health store.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --hälsa egenskapen [krävs] | Egenskapen för information om hälsa. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport möjlighet att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan övervaka tillståndet för ledigt på en nod, så att den kan rapportera ”AvailableDisk”-egenskapen på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. I health store behandlas rapporterna som separata hälsa händelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen fram hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden är\: 'Ogiltig', 'Ok', 'Varning', 'Fel', 'Okänt'. |
| --tjänst-id [krävs] | Identiteten för tjänsten. <br><br> Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den '\~' tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1', tjänstidentiteten skulle vara ' myapp\~app1\~svc1' i 6.0 + och ' myapp/app1/svc1' i tidigare versioner. |
| --käll-id [krävs] | Källnamn som identifierar komponenten klient-watchdog-systemet som genererar hälsoinformation. |
| – Beskrivning | Beskrivning av informationen om hälsa. <br><br> Den representerar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4096 tecken. Den angivna strängen är längre, trunkeras den automatiskt. När trunkerad, de sista tecknen i beskrivningen innehåller en markör ”[Truncated]” och totala strängens storlek är 4096 tecken. Förekomst av markören som anger att användare som trunkering inträffade. Observera att när trunkerad, beskrivningen har mindre än 4096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till Service Fabric-gateway som vidarebefordrar till health store. Om Immediate anges till true, rapporten skickas direkt från http-Gateway i health store, oavsett fabric-klientinställningarna som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas rapporten skickas fortfarande, till exempel om HTTP-Gateway är stängt eller meddelandet inte nå gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningar hälsa från HTTP-Gateway. Det är därför batchar enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom det tillåter hälsa klienten att optimera hälsotillståndsrapportering meddelanden till health store, samt för bearbetning. Som standard skickas rapporter inte omedelbart. |
| – ta bort när-har upphört | Värde som anger om rapporten tas bort från health store när den upphör. <br><br> Om värdet är true, rapporten har tagits bort från health store när den upphör. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). Det här sättet är personen har problem (till exempel deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
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

## <a name="sfctl-service-resolve"></a>sfctl service Lös
Lösa en Service Fabric-partition.

Lösa en partition med Service Fabric-tjänsten för att få slutpunkter repliker för tjänsten.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs] | Identiteten för tjänsten. Detta är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1”, tjänstidentiteten skulle vara ”myapp\~app1\~svc1” i 6.0 + och ”myapp/app1/svc1” i tidigare versioner. |
| --partition-key-type | Nyckeltypen för partitionen. Den här parametern krävs om partitionsschema för tjänsten är Int64Range eller namn. Möjliga värden följande. -Ingen (1) – anger att parametern PartitionKeyValue inte har angetts. Detta är giltig för partitioner med partitioneringsschema som Singleton. Detta är standardvärdet. Värdet är 1. -Int64Range (2) - anger att parametern PartitionKeyValue är en partitionsnyckel int64. Detta är giltig för partitioner med partitioneringsschema som Int64Range. Värdet är 2. -Namn (3) - anger att parametern PartitionKeyValue är ett namn för partitionen. Detta är giltig för partitioner med partitioneringsschema som namn. Värdet är 3. |
| --partition-key-value | Partitionsnyckeln. Detta krävs om partitionsschema för tjänsten är Int64Range eller namn. |
| --previous-rsp-version | Värdet i fältet Version av svar som tagits emot tidigare. Detta krävs om användaren vet att det resultat som har tagit emot tidigare är inaktuella. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-type-list"></a>sfctl service type-lista
Hämtar en lista som innehåller information om tjänsttyper som stöds av en etablerad programtyp i ett Service Fabric-kluster.

Hämtar en lista som innehåller information om tjänsttyper som stöds av en etablerad programtyp i ett Service Fabric-kluster. Den angivna programtypen måste finnas. I annat fall returneras status 404.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-typnamn [krävs] | Namnet på programtyp. |
| --program-type-version [krävs] | Versionen av programtypen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-service-update"></a>sfctl tjänstuppdatering
Uppdaterar den angivna tjänsten med hjälp av den angivna beskrivningen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tjänst-id [krävs] | ID för tjänsten. Detta ID är vanligtvis det fullständiga namnet på tjänsten utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om namnet på tjänsten är till exempel ”fabric\:/myapp/app1/svc1', tjänstidentiteten skulle vara ' myapp\~app1\~svc1' i 6.0 + och ' myapp/app1/svc1' i tidigare versioner. |
| --begränsningar | Placeringen som en sträng. Placeringen är booleskt uttryck i Egenskaper för en nod och tillåter för att begränsa en tjänst till vissa noder baserat på kraven på tjänster. Till exempel för att placera en tjänst på noder där NodeType är blå anger du följande\: ”NodeColor == blå”. |
| --korrelerade-tjänst | Namnet på Måltjänsten för korrelation med. |
| --korrelation | Korrelera tjänsten med en befintlig tjänst med hjälp av en tillhörighet för justering. |
| --antal instanser | Instansantalet. Detta gäller för tillståndslösa tjänster. |
| --belastningen mått | JSON-kodade listan över mått används när belastningsutjämning mellan noder. |
| --min---storlek på replikuppsättningen | Minsta replikuppsättningens storlek som ett tal. Den här storlek som gäller för tillståndskänsliga tjänster. |
| – Flytta kostnad | Anger flytta kostnaden för tjänsten. Möjliga värden är\: 'Noll', lågt', 'Medelhög', 'Högt'. |
| --placering principlistan | JSON-kodade lista över placeringsprinciper för tjänsten och alla associerade domännamn. Principer kan vara en eller flera av\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --kvorum-förlust-vänta | Maximal varaktighet i sekunder som en partition kan vara i tillståndet förlorar kvorum. Varaktigheten gäller för tillståndskänsliga tjänster. |
| --Vänta-replik-omstart | Varaktighet i sekunder mellan när en replik kraschar och när en replik skapas. Varaktigheten gäller för tillståndskänsliga tjänster. |
| --skalning principer | JSON-kodade lista över skalning principer för den här tjänsten. |
| --vänteläge av repliken keep | Maximal varaktighet i sekunder, för vilka vänteläge bevaras repliker innan den tas bort. Varaktigheten gäller för tillståndskänsliga tjänster. |
| --stateful | Anger Måltjänsten är en tillståndskänslig service. |
| --tillståndslös | Anger Måltjänsten är en tillståndslös tjänst. |
| ---replik-set-Målstorlek | Replikuppsättningens Ange storlek som ett tal. Den här storlek som gäller för tillståndskänsliga tjänster. |
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
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
