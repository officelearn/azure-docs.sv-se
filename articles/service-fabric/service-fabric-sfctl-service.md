---
title: Azure Service Fabric CLI- sfctl-tjänst
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista över kommandon för hantering av tjänster, tjänsttyper och servicepaket.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 696de713129ca71dd7f2451501a7cc9eca0ee9b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906234"
---
# <a name="sfctl-service"></a>sfctl service
Skapa, ta bort och hantera tjänst, tjänsttyper och servicepaket.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| app-namn | Hämtar namnet på Service Fabric-programmet för en tjänst. |
| lista över kodpaket | Hämtar listan över kodpaket som distribueras på en Service Fabric-nod. |
| skapa | Skapar den angivna tjänsten Service Fabric. |
| delete | Tar bort en befintlig Service Fabric-tjänst. |
| distribuerad typ | Hämtar information om en angiven tjänsttyp för programmet som distribueras på en nod i ett Service Fabric-kluster. |
| distribuerad-typ-lista | Hämtar listan som innehåller information om tjänsttyper från de program som distribueras på en nod i ett Service Fabric-kluster. |
| description | Hämtar beskrivningen av en befintlig Service Fabric-tjänst. |
| få-behållare-loggar | Hämtar behållarloggarna för behållare som distribueras på en Service Fabric-nod. |
| Hälsa | Hämtar hälsotillståndet för den angivna Service fabric-tjänsten. |
| information | Hämtar information om den specifika tjänst som tillhör Programmet Service Fabric. |
| lista | Hämtar information om alla tjänster som tillhör programmet som anges av program-ID. |
| manifest | Hämtar manifestet som beskriver en tjänsttyp. |
| paket-distribuera | Hämtar paket som är associerade med angivet tjänstmanifest till avbildningscachen på angiven nod. |
| paket-hälsa | Hämtar information om hälsotillståndet för ett tjänstpaket för ett visst program som distribueras för en Service Fabric-nod och ett program. |
| paket-info | Hämtar listan över tjänstpaket som distribueras på en Service Fabric-nod som matchar exakt det angivna namnet. |
| paketlista | Hämtar listan över tjänstpaket som distribueras på en Service Fabric-nod. |
| recover | Anger för servicetygsklustret att det ska försöka återställa den angivna tjänsten som för närvarande har fastnat i kvorumförlust. |
| rapport-hälsa | Skickar en hälsorapport om tjänsten Service Fabric. |
| Lösa | Lös en Partition för service fabric. |
| typlista | Hämtar listan som innehåller information om tjänsttyper som stöds av en etablerad programtyp i ett Service Fabric-kluster. |
| update | Uppdaterar den angivna tjänsten med den angivna uppdateringsbeskrivningen. |

## <a name="sfctl-service-app-name"></a>sfctl tjänst app-namn
Hämtar namnet på Service Fabric-programmet för en tjänst.

Hämtar namnet på programmet för den angivna tjänsten. Ett fel på 404 FABRIC_E_SERVICE_DOES_NOT_EXIST returneras om det inte finns någon tjänst med det angivna service-ID:t.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl tjänstkod-paket-lista
Hämtar listan över kodpaket som distribueras på en Service Fabric-nod.

Hämtar listan över kodpaket som distribueras på en Service Fabric-nod för det angivna programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --kod-paket-namn | Namnet på kodpaketet som anges i tjänstmanifestet som registrerats som en del av en programtyp i ett Service Fabric-kluster. |
| --service-manifest-namn | Namnet på ett tjänstmanifest som registrerats som en del av en programtyp i ett Service Fabric-kluster. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-create"></a>sfctl-tjänsten skapar
Skapar den angivna tjänsten Service Fabric.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med tecknet ' ' . Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och 'myapp/app1' i tidigare versioner. |
| --namn [Obligatoriskt] | Tjänstens namn. Detta bör vara ett underordnat program-ID. Det här är det `fabric\:` fullständiga namnet inklusive URI:n. Till exempel `fabric\:/A/B` tjänst är `fabric\:/A`ett underordnat program . |
| --service-typ [Obligatoriskt] | Namnet på tjänsttypen. |
| --aktivering-läge | Aktiveringsläget för servicepaketet. |
| --begränsningar | Placeringsbegränsningarna som en sträng. Placeringsbegränsningar är booleska uttryck för nodegenskaper och gör det möjligt att begränsa en tjänst till vissa noder baserat på servicekraven. Om du till exempel vill placera en tjänst på noder där NodeType är blå anger du följande\:"NodeColor == blue". |
| --korrelerad-tjänst | Namn på måltjänsten att korrelera med. |
| --korrelation | Korrelera tjänsten med en befintlig tjänst med hjälp av en justeringstillhörighet. |
| --dns-namn | DNS-namnet på den tjänst som ska skapas. Tjänsten Service Fabric DNS-system måste vara aktiverad för den här inställningen. |
| --instans-antal | Instansen räknas. Detta gäller endast tillståndslösa tjänster. |
| --int-system | Anger att tjänsten ska vara jämnt partitionerad över ett intervall av osignerade heltal. |
| --int-scheme-count --int-scheme-count --int-scheme-count -- | Antalet partitioner i heltalstangentintervallet som ska skapas, om du använder ett enhetligt heltalspartitionsschema. |
| --int-system-hög | Slutet av nyckel heltalsområdet, om du använder ett enhetligt heltalspartitionsschema. |
| --int-scheme-låg | Början av nyckel heltalsområdet, om du använder ett enhetligt heltalspartitionsschema. |
| --load-metrics | JSON kodade lista över mått som används vid belastningsutjämningstjänster över noder. |
| --min-replica-set-storlek | Minsta replikuppsättningsstorlek som ett tal. Detta gäller endast tillståndskänsliga tjänster. |
| --move-kostnad | Anger flyttkostnaden för tjänsten. Möjliga värden\: är "Noll", "Låg", "Medium", "Hög", "VeryHigh". |
| --namngivna-schema | Anger att tjänsten ska ha flera namngivna partitioner. |
| --namngiven schemalista | JSON kodade lista med namn att partitionera tjänsten över, om du använder det namngivna partitionsschemat. |
| --ingen-ihållande-tillstånd | Om värdet är true anger detta att tjänsten inte har något beständigt tillstånd lagrat på den lokala disken, eller så lagrar den bara tillstånd i minnet. |
| --placement-policy-lista | JSON kodade lista över placeringsprinciper för tjänsten och eventuella associerade domännamn. Principer kan vara en\: `NonPartiallyPlaceService` `PreferPrimaryDomain`eller `RequireDomain` `RequireDomainDistribution`flera av , , , . |
| --kvorum-förlust-vänta | Den maximala varaktigheten, i sekunder, för vilken en partition tillåts vara i ett tillstånd av kvorumförlust. Detta gäller endast tillståndskänsliga tjänster. |
| --replica-restart-wait --replica-restart-wait --replica-restart-wait -- | Varaktigheten, i sekunder, mellan när en replik går ner och när en ny replik skapas. Detta gäller endast tillståndskänsliga tjänster. |
| --skala-principer | JSON kodade lista över skalningsprinciper för den här tjänsten. |
| --service-placement-tid | Varaktigheten för vilka repliker kan stanna i InBuild innan du rapporterar att bygga har fastnat. Detta gäller endast tillståndskänsliga tjänster. |
| --singleton-system | Anger att tjänsten ska ha en enda partition eller vara en icke-partitionerad tjänst. |
| --stand-by-replica-keep | Den maximala varaktigheten, i sekunder, för vilken StandBy-repliker kommer att underhållas innan de tas bort. Detta gäller endast tillståndskänsliga tjänster. |
| --stateful | Anger att tjänsten är en tillståndskänslig tjänst. |
| --statslös | Anger att tjänsten är en tillståndslös tjänst. |
| --target-replica-set-size -target-replica-set-size -target-replica-set-size --target- | Målrepliken anger storlek som ett tal. Detta gäller endast tillståndskänsliga tjänster. |
| --timeout -t | Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-delete"></a>sfctl-tjänsten ta bort
Tar bort en befintlig Service Fabric-tjänst.

En tjänst måste skapas innan den kan tas bort. Som standard försöker Service Fabric stänga tjänstrepliker på ett graciöst sätt och sedan ta bort tjänsten. Men om tjänsten har problem med att stänga repliken på ett smidigt sätt kan borttagningsåtgärden ta lång tid eller fastna. Använd den valfria ForceRemove-flaggan för att hoppa över den graciösa stängningssekvensen och kraftfullt ta bort tjänsten.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --force-remove | Ta bort ett Service Fabric-program eller en tjänst med kraft utan att gå igenom den graciösa avstängningssekvensen. Den här parametern kan användas för att kraftfullt ta bort ett program eller en tjänst som tar bort är tidsgränsen på grund av problem i tjänstkoden som förhindrar graciös stängning av repliker. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-deployed-type"></a>sfctl-tjänst distribuerad typ
Hämtar information om en angiven tjänsttyp för programmet som distribueras på en nod i ett Service Fabric-kluster.

Hämtar listan som innehåller information om en viss tjänsttyp från de program som distribueras på en nod i ett Service Fabric-kluster. Svaret innehåller namnet på tjänsttypen, dess registreringsstatus, kodpaketet som registrerade det och aktiverings-ID för servicepaketet. Varje post representerar en aktivering av en tjänsttyp, differentierad av aktiverings-ID.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --service-type-name [Obligatoriskt] | Anger namnet på en tjänsttyp för Service Fabric. |
| --service-manifest-namn | Namnet på tjänstmanifestet för att filtrera listan över distribuerad tjänsttypsinformation. Om det anges innehåller svaret bara information om tjänsttyper som har definierats i det här tjänstmanifestet. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl tjänst distribuerad-typ-lista
Hämtar listan som innehåller information om tjänsttyper från de program som distribueras på en nod i ett Service Fabric-kluster.

Hämtar listan som innehåller information om tjänsttyper från de program som distribueras på en nod i ett Service Fabric-kluster. Svaret innehåller namnet på tjänsttypen, dess registreringsstatus, kodpaketet som registrerade det och aktiverings-ID för servicepaketet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --service-manifest-namn | Namnet på tjänstmanifestet för att filtrera listan över distribuerad tjänsttypsinformation. Om det anges innehåller svaret bara information om tjänsttyper som har definierats i det här tjänstmanifestet. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-description"></a>sfctl tjänst beskrivning
Hämtar beskrivningen av en befintlig Service Fabric-tjänst.

Hämtar beskrivningen av en befintlig Service Fabric-tjänst. En tjänst måste skapas innan dess beskrivning kan erhållas.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl service get-container-loggar
Hämtar behållarloggarna för behållare som distribueras på en Service Fabric-nod.

Hämtar behållarloggarna för behållare som distribueras på en Service Fabric-nod för det angivna kodpaketet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --kod-paket-namn [Obligatoriskt] | Namnet på kodpaketet som anges i tjänstmanifestet som registrerats som en del av en programtyp i ett Service Fabric-kluster. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --service-manifest-namn [Obligatoriskt] | Namnet på ett tjänstmanifest som registrerats som en del av en programtyp i ett Service Fabric-kluster. |
| --föregående | Anger om behållarloggar ska hämtas från utsända/döda behållare för kodpaketinstansen. |
| --svans | Antal rader som ska visas från slutet av loggarna. Standard är 100. "alla" för att visa hela loggarna. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-health"></a>sfctl tjänsten hälsa
Hämtar hälsotillståndet för den angivna Service fabric-tjänsten.

Hämtar hälsoinformationen för den angivna tjänsten. Använd EventsHealthStateFilter för att filtrera insamlingen av hälsohändelser som rapporterats på tjänsten baserat på hälsotillståndet. Använd PartitionsHealthStateFilter för att filtrera samlingen av partitioner som returneras. Om du anger en tjänst som inte finns i hälsoarkivet returnerar den här begäran ett fel.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --events-health-state-filter | Gör det möjligt att filtrera samlingen av HealthEvent-objekt som returneras baserat på hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden, som erhålls med hjälp av operatorn "ELLER". Om det angivna värdet till exempel är 6 returneras alla händelser med HealthState-värdet OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --exklude-hälso-statistik | Anger om hälsostatistiken ska returneras som en del av frågeresultatet. Falskt som standard. Statistiken visar antalet underordnade entiteter i hälsotillståndet Ok, Varning och Fel. |
| --partitioner-hälsotillstånd-filter | Tillåter filtrering av hälsotillståndsobjekt för partitioner som returneras i resultatet av tjänsthälsofrågan baserat på deras hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast partitioner som matchar filtret returneras. Alla partitioner används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden som erhålls med hjälp av bitvis "ELLER"-operator. Om värdet till exempel är 6 returneras hälsotillståndet för partitioner med HealthState-värdet OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-info"></a>sfctl tjänst info
Hämtar information om den specifika tjänst som tillhör Programmet Service Fabric.

Returnerar informationen om den angivna tjänsten som tillhör det angivna Service Fabric-programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
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

## <a name="sfctl-service-list"></a>sfctl-tjänstlista
Hämtar information om alla tjänster som tillhör programmet som anges av program-ID.

Returnerar informationen om alla tjänster som hör till programmet som anges av program-ID.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --fortsättning-token | Parametern för fortsättningstoken används för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tomt värde inkluderas i svaret på API:et när resultaten från systemet inte får plats i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API:et nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller fortsättningstoken inget värde. Värdet för den här parametern bör inte URL-kodas. |
| --service-type-name --service-type-name --service-type-name -- | Det tjänsttypsnamn som används för att filtrera de tjänster som ska efterfrågas. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-manifest"></a>sfctl tjänst manifest
Hämtar manifestet som beskriver en tjänsttyp.

Hämtar manifestet som beskriver en tjänsttyp. Svaret innehåller tjänstmanifestet XML som en sträng.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-type-name [Obligatoriskt] | Namnet på programtypen. |
| --application-type-version [Obligatoriskt] | Versionen av programtypen. |
| --service-manifest-namn [Obligatoriskt] | Namnet på ett tjänstmanifest som registrerats som en del av en programtyp i ett Service Fabric-kluster. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-package-deploy"></a>sfctl tjänst paket-distribuera
Hämtar paket som är associerade med angivet tjänstmanifest till avbildningscachen på angiven nod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-type-name [Obligatoriskt] | Namnet på programmanifestet för motsvarande begärda tjänstmanifest. |
| --app-type-version [Obligatoriskt] | Versionen av programmanifestet för motsvarande begärda tjänstmanifest. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --service-manifest-namn [Obligatoriskt] | Namnet på tjänstmanifestet som är associerat med paketen som ska hämtas. |
| --share-policy | JSON kodade lista över delningsprinciper. Varje delningsprincipelement består av ett "namn" och "omfattning". Namnet motsvarar namnet på koden, konfigurationen eller datapaketet som ska delas. Tillämpningsområdet kan vara antingen "Ingen", "Alla", "Kod", "Config" eller "Data". |
| --timeout -t | Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-package-health"></a>sfctl servicepaket-hälsa
Hämtar information om hälsotillståndet för ett tjänstpaket för ett visst program som distribueras för en Service Fabric-nod och ett program.

Hämtar information om hälsotillståndet för ett tjänstpaket för ett visst program som distribueras på en Service Fabric-nod. Använd EventsHealthStateFilter för att filtrera för insamlingen av HealthEvent-objekt som rapporterats på det distribuerade tjänstpaketet baserat på hälsotillstånd.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --service-paket-namn [Obligatoriskt] | Namnet på servicepaketet. |
| --events-health-state-filter | Gör det möjligt att filtrera samlingen av HealthEvent-objekt som returneras baserat på hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden, som erhålls med hjälp av operatorn "ELLER". Om det angivna värdet till exempel är 6 returneras alla händelser med HealthState-värdet OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-package-info"></a>sfctl tjänst paket-info
Hämtar listan över tjänstpaket som distribueras på en Service Fabric-nod som matchar exakt det angivna namnet.

Returnerar informationen om tjänstpaketen som distribueras på en Service Fabric-nod för det angivna programmet. Dessa resultat är av servicepaket vars namn matchar exakt det tjänstpaketnamn som anges som parameter.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --service-paket-namn [Obligatoriskt] | Namnet på servicepaketet. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-package-list"></a>sfctl tjänst paket-lista
Hämtar listan över tjänstpaket som distribueras på en Service Fabric-nod.

Returnerar informationen om tjänstpaketen som distribueras på en Service Fabric-nod för det angivna programmet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-recover"></a>sfctl tjänsten återställa
Anger för servicetygsklustret att det ska försöka återställa den angivna tjänsten som för närvarande har fastnat i kvorumförlust.

Anger för servicetygsklustret att det ska försöka återställa den angivna tjänsten som för närvarande har fastnat i kvorumförlust. Den här åtgärden bör endast utföras om det är känt att replikerna som är nere inte kan återställas. Felaktig användning av detta API kan orsaka potentiell dataförlust.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>sfctl service report-health sfctl service report-health sfctl service report-health sfc
Skickar en hälsorapport om tjänsten Service Fabric.

Rapporterar hälsotillståndet för den angivna tjänsten Service Fabric. Rapporten måste innehålla information om källan till hälsorapporten och egenskapen som den rapporteras till. Rapporten skickas till en service fabric gateway service, som vidarebefordras till hälsoarkivet. Rapporten kan accepteras av gatewayen, men avvisas av hälsoarkivet efter extra validering. Hälsoarkivet kan till exempel avvisa rapporten på grund av en ogiltig parameter, till exempel ett inaktuellt sekvensnummer. Om du vill se om rapporten har tillämpats i hälsoarkivet kontrollerar du att rapporten visas i tjänstens hälsohändelser.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --health-property [Obligatoriskt] | Egenskapen av hälsoinformationen. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning för att tillåta reporterflexibilitet att kategorisera tillståndsvillkoret som utlöser rapporten. En reporter med SourceId "LocalWatchdog" kan till exempel övervaka tillståndet för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på den noden. Samma reporter kan övervaka nodanslutningen, så att den kan rapportera en egenskap "Anslutning" på samma nod. I hälsoarkivet behandlas dessa rapporter som separata hälsohändelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen hälsoinformationen unikt. |
| --hälsotillstånd [Krävs] | Möjliga värden\: är "Ogiltig", "Ok", "Varning", "Fel", "Okänd". |
| --service-id [Obligatoriskt] | Tjänstens identitet. <br><br> Detta är vanligtvis det fullständiga namnet på\:tjänsten utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med tecknet ' ' . Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och 'myapp/app1/svc1' i tidigare versioner. |
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

## <a name="sfctl-service-resolve"></a>sfctl-tjänsten lösa
Lös en Partition för service fabric.

Lös en tjänstpartition för service fabric för att hämta slutpunkterna för tjänstrepliker.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta ID är vanligtvis det fullständiga namnet\:på tjänsten utan URI-schemat för tyg. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och "myapp/app1/svc1" i tidigare versioner. |
| --partition-nyckel-typ | Nyckeltyp för partitionen. Den här parametern krävs om partitionsschemat för tjänsten är Int64Range eller Named. De möjliga värdena följer. - Ingen (1) - Anger att parametern PartitionKeyValue inte har angetts. Detta gäller för partitioner med partitioneringsschema som Singleton. Detta är standardvärdet. Värdet är 1. - Int64Range (2) - Anger att parametern PartitionKeyValue är en int64-partitionsnyckel. Detta gäller för partitioner med partitioneringsschema som Int64Range. Värdet är 2. - Namngivet (3) - Anger att parametern PartitionKeyValue är ett namn på partitionen. Detta gäller för partitioner med partitioneringsschema som namngivet. Värdet är 3. |
| --partition-nyckel-värde | Partitionsnyckel. Detta krävs om partitionsschemat för tjänsten är Int64Range eller Named. Detta är inte partitions-ID, utan snarare antingen heltalsnyckelvärdet eller namnet på partitions-ID. Om din tjänst till exempel använder varierade partitioner från 0 till 10, skulle de PartitionKeyValue vara ett heltal i det intervallet. Frågetjänstbeskrivning för att se intervallet eller namnet. |
| --föregående-rsp-version | Värdet i fältet Version för svaret som togs emot tidigare. Detta krävs om användaren vet att resultatet som har fåtts tidigare är inaktuellt. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-type-list"></a>sfctl tjänsttyp-lista
Hämtar listan som innehåller information om tjänsttyper som stöds av en etablerad programtyp i ett Service Fabric-kluster.

Hämtar listan som innehåller information om tjänsttyper som stöds av en etablerad programtyp i ett Service Fabric-kluster. Den angivna programtypen måste finnas. Annars returneras en 404-status.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-type-name [Obligatoriskt] | Namnet på programtypen. |
| --application-type-version [Obligatoriskt] | Versionen av programtypen. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-service-update"></a>sfctl tjänst uppdatering
Uppdaterar den angivna tjänsten med den angivna uppdateringsbeskrivningen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --service-id [Obligatoriskt] | Tjänstens identitet. Detta är vanligtvis det fullständiga namnet på\:tjänsten utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om tjänstnamnet till exempel är\:"fabric /myapp/app1/svc1", skulle tjänstidentiteten vara "myapp\~app1\~svc1" i 6.0+ och 'myapp/app1/svc1' i tidigare versioner. |
| --begränsningar | Placeringsbegränsningarna som en sträng. Placeringsbegränsningar är booleska uttryck för nodegenskaper och gör det möjligt att begränsa en tjänst till vissa noder baserat på servicekraven. Om du till exempel vill placera en tjänst på noder där NodeType är blå anger du följande\: "NodeColor == blue". |
| --korrelerad-tjänst | Namn på måltjänsten att korrelera med. |
| --korrelation | Korrelera tjänsten med en befintlig tjänst med hjälp av en justeringstillhörighet. |
| --instans-antal | Instansen räknas. Detta gäller endast tillståndslösa tjänster. |
| --load-metrics | JSON kodade lista över mått som används vid belastningsutjämning över noder. |
| --min-replica-set-storlek | Minsta replikuppsättningsstorlek som ett tal. Detta gäller endast tillståndskänsliga tjänster. |
| --move-kostnad | Anger flyttkostnaden för tjänsten. Möjliga värden\: är "Noll", "Låg", "Medium", "Hög", "VeryHigh". |
| --placement-policy-lista | JSON kodade lista över placeringsprinciper för tjänsten och eventuella associerade domännamn. Principer kan vara en\: `NonPartiallyPlaceService` `PreferPrimaryDomain`eller `RequireDomain` `RequireDomainDistribution`flera av , , , . |
| --kvorum-förlust-vänta | Den maximala varaktigheten, i sekunder, för vilken en partition tillåts vara i ett tillstånd av kvorumförlust. Detta gäller endast tillståndskänsliga tjänster. |
| --replica-restart-wait --replica-restart-wait --replica-restart-wait -- | Varaktigheten, i sekunder, mellan när en replik går ner och när en ny replik skapas. Detta gäller endast tillståndskänsliga tjänster. |
| --skala-principer | JSON kodade lista över skalningsprinciper för den här tjänsten. |
| --service-placement-tid | Varaktigheten för vilka repliker kan stanna i InBuild innan du rapporterar att bygga har fastnat. Detta gäller endast tillståndskänsliga tjänster. |
| --stand-by-replica-keep | Den maximala varaktigheten, i sekunder, för vilken StandBy-repliker kommer att underhållas innan de tas bort. Detta gäller endast tillståndskänsliga tjänster. |
| --stateful | Anger att måltjänsten är en tillståndskänslig tjänst. |
| --statslös | Anger att måltjänsten är en tillståndslös tjänst. |
| --target-replica-set-size -target-replica-set-size -target-replica-set-size --target- | Målrepliken anger storlek som ett tal. Detta gäller endast tillståndskänsliga tjänster. |
| --timeout -t | Standard\: 60. |

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
