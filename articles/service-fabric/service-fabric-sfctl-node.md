---
title: AZURE Service Fabric CLI- sfctl-nod
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för hantering av klusternoder.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 5881e6485003abd4fd23a7f6d06a428e768c00fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905877"
---
# <a name="sfctl-node"></a>sfctl node
Hantera noderna som bildar ett kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| add-configuration-parameter-overrides | Lägger till listan över konfigurationsidosättningar på den angivna noden. |
| inaktivera | Inaktivera en klusternod för tjänst fabric med den angivna inaktiveringsavsikten. |
| Aktivera | Aktivera en klusternod för service fabric som för närvarande är inaktiverad. |
| få-konfiguration-åsidosättningar | Hämtar listan över konfigurationsidosättningar på den angivna noden. |
| Hälsa | Hämtar hälsotillståndet för en Service Fabric-nod. |
| information | Hämtar information om en viss nod i Service Fabric-klustret. |
| lista | Hämtar listan över noder i servicetygsklustret. |
| läsa in | Hämtar inläsningsinformationen för en Service Fabric-nod. |
| ta bort-konfiguration-åsidosättningar | Tar bort konfigurationsidosättningar på den angivna noden. |
| ta bort-tillstånd | Meddelar Service Fabric att det kvarstående tillståndet på en nod har tagits bort eller gått förlorat permanent. |
| rapport-hälsa | Skickar en hälsorapport på noden Service Fabric. |
| restart | Startar om en klusternod för Service Fabric. |
| Övergången | Startar eller stoppar en klusternod. |
| övergångsstatus | Hämtar förloppet för en åtgärd som har startats med StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl nod add-configuration-parameter-overrides
Lägger till listan över konfigurationsidosättningar på den angivna noden.

Med det här api:et kan du lägga till alla befintliga konfigurations åsidosättningar på den angivna noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --config-parameter-override-list [Obligatoriskt] | Beskrivning för att lägga till lista över konfigurationsidosättningar. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --kraft | Tvinga till konfigurationsidosättning på angivna noder. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-node-disable"></a>sfctl-nod inaktivera
Inaktivera en klusternod för tjänst fabric med den angivna inaktiveringsavsikten.

Inaktivera en klusternod för tjänst fabric med den angivna inaktiveringsavsikten. När avaktiveringen pågår kan avaktiveringsavsikten ökas, men inte minskas (till exempel kan en nod som inaktiveras med pause-avsikten inaktiveras ytterligare med Omstart, men inte tvärtom. Noder kan återaktiveras med hjälp av åtgärden Aktivera en nod när som helst efter att de har inaktiverats. Om inaktiveringen inte är klar avbryts avaktiveringen. En nod som går ner och kommer tillbaka upp medan inaktiveras måste fortfarande återaktiveras innan tjänster placeras på den noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --deaktivering-avsikt | Beskriver avsikten eller orsaken till att noden har inaktiverats. De möjliga värdena följer. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-node-enable"></a>sfctl-nod aktivera
Aktivera en klusternod för service fabric som för närvarande är inaktiverad.

Aktiverar en klusternod för service fabric som för närvarande är inaktiverad. När noden har aktiverats blir noden återigen ett lönsamt mål för att placera nya repliker, och alla inaktiverade repliker som finns kvar på noden kommer att återaktiveras.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-node-get-configuration-overrides"></a>sfctl nod get-configuration-overrides
Hämtar listan över konfigurationsidosättningar på den angivna noden.

Med det här api:et kan alla befintliga konfigurations åsidosättningar på den angivna noden hämtas.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-node-health"></a>sfctl nod hälsa
Hämtar hälsotillståndet för en Service Fabric-nod.

Hämtar hälsotillståndet för en Service Fabric-nod. Använd EventsHealthStateFilter för att filtrera insamlingen av hälsohändelser som rapporterats på noden baserat på hälsotillståndet. Om noden som du anger med namn inte finns i hälsoarkivet returneras ett fel.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
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

## <a name="sfctl-node-info"></a>sfctl-nodinformation
Hämtar information om en viss nod i Service Fabric-klustret.

Svaret innehåller namn, status, ID, hälsa, drifttid och annan information om noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-node-list"></a>sfctl-nodlista
Hämtar listan över noder i servicetygsklustret.

Svaret innehåller namn, status, ID, hälsa, drifttid och annan information om noderna.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --fortsättning-token | Parametern för fortsättningstoken används för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tomt värde inkluderas i svaret på API:et när resultaten från systemet inte får plats i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API:et nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller fortsättningstoken inget värde. Värdet för den här parametern bör inte URL-kodas. |
| --max-resultat | Det maximala antalet resultat som ska returneras som en del av de växlingsdelade frågorna. Den här parametern definierar den övre gränsen för antalet returnerade resultat. De returnerade resultaten kan vara mindre än de angivna maximala resultaten om de inte får plats i meddelandet enligt de begränsningar för högsta meddelandestorlek som definierats i konfigurationen. Om den här parametern är noll eller inte angiven innehåller den växlingsbara frågan så många resultat som möjligt som passar i returmeddelandet. |
| --nod-status-filter | Gör det möjligt att filtrera noderna baserat på NodeStatus. Endast de noder som matchar det angivna filtervärdet returneras. Filtervärdet kan vara något av följande.  Standardstandard.\: |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-node-load"></a>sfctl nod belastning
Hämtar inläsningsinformationen för en Service Fabric-nod.

Hämtar inläsningsinformationen för en Service Fabric-nod för alla mått som har inläsning eller kapacitet definierad.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-node-remove-configuration-overrides"></a>sfctl nod ta bort-konfiguration-åsidosättningar
Tar bort konfigurationsidosättningar på den angivna noden.

Med det här api:et kan alla befintliga konfigurations åsidosättningar tas bort på angiven nod.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-node-remove-state"></a>sfctl nod remove-state
Meddelar Service Fabric att det kvarstående tillståndet på en nod har tagits bort eller gått förlorat permanent.

Detta innebär att det inte är möjligt att återställa det kvarstående tillståndet för den noden. Detta händer vanligtvis om en hårddisk har rensats, eller om en hårddisk kraschar. Noden måste vara nere för att åtgärden ska lyckas. Den här åtgärden gör att Service Fabric vet att replikerna på den noden inte längre finns och att Service Fabric ska sluta vänta på att replikerna ska komma upp igen. Kör inte den här cmdleten om tillståndet på noden inte har tagits bort och noden kan komma tillbaka med sitt tillstånd intakt. Från Och med Service Fabric 6.5, för att använda det här API:et för frönoder, ändra seed-noderna till vanliga (icke-frö) noder och anropa sedan det här API:et för att ta bort nodtillståndet. Om klustret körs på Azure, när seed-noden går ned, försöker Service Fabric ändra den till en nod som inte är seed-noden automatiskt. För att göra detta, se till att antalet icke-utsäde noder i den primära nodtypen är inte mindre än antalet Down seed-noder. Om det behövs lägger du till fler noder i den primära nodtypen för att uppnå detta. Om noden Down-seed inte förväntas komma tillbaka med dess tillstånd intakt, ta bort noden från\:klustret, se https //docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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

## <a name="sfctl-node-report-health"></a>sfctl nod rapport-hälsa
Skickar en hälsorapport på noden Service Fabric.

Rapporterar hälsotillståndet för den angivna service fabric-noden. Rapporten måste innehålla information om källan till hälsorapporten och egenskapen som den rapporteras till. Rapporten skickas till en Gateway-nod för Service Fabric som vidarebefordras till hälsoarkivet. Rapporten kan accepteras av gatewayen, men avvisas av hälsoarkivet efter extra validering. Hälsoarkivet kan till exempel avvisa rapporten på grund av en ogiltig parameter, till exempel ett inaktuellt sekvensnummer. Om du vill se om rapporten har tillämpats i hälsoarkivet kontrollerar du att rapporten visas i avsnittet HealthEvents.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --health-property [Obligatoriskt] | Egenskapen av hälsoinformationen. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning för att tillåta reporterflexibilitet att kategorisera tillståndsvillkoret som utlöser rapporten. En reporter med SourceId "LocalWatchdog" kan till exempel övervaka tillståndet för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på den noden. Samma reporter kan övervaka nodanslutningen, så att den kan rapportera en egenskap "Anslutning" på samma nod. I hälsoarkivet behandlas dessa rapporter som separata hälsohändelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen hälsoinformationen unikt. |
| --hälsotillstånd [Krävs] | Möjliga värden\: är "Ogiltig", "Ok", "Varning", "Fel", "Okänd". |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
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

## <a name="sfctl-node-restart"></a>sfctl nod omstart
Startar om en klusternod för Service Fabric.

Startar om en klusternod för Service Fabric som redan har startats.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --create-fabric-dump --create-fabric-dump --create-fabric-dump -- | Ange Sant om du vill skapa en dump av tygnodprocessen. Värdet är skiftlägeskänsligt.  Standard\: falskt. |
| --nod-instans-id | Instans-ID för målnoden. Om instans-ID anges startas noden bara om den matchar med den aktuella instansen av noden. Ett standardvärde på "0" matchar alla instans-ID. Instans-ID kan erhållas med hjälp av hämta nodfråga.  Standard\: 0. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-node-transition"></a>övergång till sfctl-nod
Startar eller stoppar en klusternod.

Startar eller stoppar en klusternod.  En klusternod är en process, inte själva OS-instansen.  Om du vill starta en nod skickar du in "Start" för parametern NodeTransitionType. Om du vill stoppa en nod skickar du in "Stopp" för parametern NodeTransitionType. Det här API:et startar åtgärden - när API:et returnerar noden kanske inte har slutförts ännu. Anropa GetNodeTransitionProgress med samma OperationId för att få förloppet för åtgärden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --nod-instans-id [Obligatoriskt] | Målnodens nodinstans-ID. Detta kan bestämmas via GetNodeInfo API. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --nod-övergång-typ [Obligatoriskt] | Anger vilken typ av övergång som ska utföras.  NodeTransitionType.Start startar en stoppad nod. NodeTransitionType.Stop stoppar en nod som är uppe. |
| --operation-id [Obligatoriskt] | Ett GUID som identifierar ett anrop av det här API:et.  Detta skickas till motsvarande GetProgress API. |
| --stop-duration-in-seconds [Obligatoriskt] | Varaktigheten, i sekunder, för att hålla noden stoppad.  Minimivärdet är 600, det högsta är 14400.  När den här tiden har gått ut kommer noden automatiskt att komma upp igen. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-node-transition-status"></a>övergångsstatus för sfctl-nod
Hämtar förloppet för en åtgärd som har startats med StartNodeTransition.

Får förloppet för en åtgärd som startats med StartNodeTransition med hjälp av den angivna OperationId.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --operation-id [Obligatoriskt] | Ett GUID som identifierar ett anrop av det här API:et.  Detta skickas till motsvarande GetProgress API. |
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