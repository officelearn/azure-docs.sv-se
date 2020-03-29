---
title: AZURE Service Fabric CLI- sfctl-kluster
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för hantering av kluster.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 007ad6f59f0ce304db579f4faa1bb95611a93a37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906150"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Välj, hantera och använd Service Fabric-kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| kod-versioner | Hämtar en lista över fabric-kodversioner som är etablerade i ett Service Fabric-kluster. |
| config-versioner | Hämtar en lista över infrastrukturkonfigurationsversioner som är etablerade i ett Service Fabric-kluster. |
| Hälsa | Hämtar hälsotillståndet för ett service fabric-kluster. |
| manifest | Hämta klustermanifestet För servicetyg. |
| operation-avbryt | Avbryter en felåtgärd som orsakas av användaren. |
| operation-lista | Hämtar en lista över användarinducerade felåtgärder filtrerade efter medföljande indata. |
| Bestämmelse | Etablera kod- eller konfigurationspaket för ett Service Fabric-kluster. |
| återställningssystem | Anger för servicetygsklustret att det ska försöka återställa de systemtjänster som för närvarande har fastnat i kvorumförlust. |
| rapport-hälsa | Skickar en hälsorapport i service fabric-klustret. |
| välj | Ansluter till en service fabric-klusterslutpunkt. |
| visa-anslutning | Visa vilket Service Fabric-kluster som den här sfctl-instansen är ansluten till. |
| oetablera | Avetablera kod- eller konfigurationspaket för ett Service Fabric-kluster. |
| uppgradera | Börja uppgradera koden eller konfigurationsversionen av ett Service Fabric-kluster. |
| uppgradering-återuppta | Gör klusteruppgraderingen gå vidare till nästa uppgraderingsdomän. |
| återställning av uppgradering | Återställ uppgraderingen av ett Service Fabric-kluster. |
| uppgraderingsstatus | Hämtar förloppet för den aktuella klusteruppgraderingen. |
| uppgradering-uppdatering | Uppdatera uppgraderingsparametrarna för en uppgradering av Service Fabric-kluster. |

## <a name="sfctl-cluster-code-versions"></a>sfctl kluster kod-versioner
Hämtar en lista över fabric-kodversioner som är etablerade i ett Service Fabric-kluster.

Hämtar en lista med information om fabric-kodversioner som är etablerade i klustret. Parametern CodeVersion kan användas för att eventuellt filtrera utdata till endast den aktuella versionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kod-version | Produktversionen av Service Fabric. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-config-versions"></a>sfctl kluster config-versioner
Hämtar en lista över infrastrukturkonfigurationsversioner som är etablerade i ett Service Fabric-kluster.

Hämtar en lista med information om infrastrukturkonfigurationsversioner som är etablerade i klustret. Parametern ConfigVersion kan användas för att eventuellt filtrera utdata till endast den aktuella versionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --config-version | Config-versionen av Service Fabric. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-health"></a>sfctl kluster hälsa
Hämtar hälsotillståndet för ett service fabric-kluster.

Använd EventsHealthStateFilter för att filtrera insamlingen av hälsohändelser som rapporterats i klustret baserat på hälsotillståndet. På samma sätt använder du NodesHealthStateFilter och ApplicationsHealthStateFilter för att filtrera insamlingen av noder och program som returneras baserat på deras aggregerade hälsotillstånd.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --applications-health-state-filter | Gör det möjligt att filtrera programhälsotillståndsobjekt som returneras i resultatet av klusterhälsofråga baserat på deras hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärde som erhållits från medlemmar eller bitvisåtgärder på medlemmar i HealthStateFilter-uppräkning. Endast program som matchar filtret returneras. Alla program används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden som erhålls med hjälp av bitvis "ELLER"-operator. Om värdet till exempel är 6 returneras hälsotillståndet för program med HealthState-värdet OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --events-health-state-filter | Gör det möjligt att filtrera samlingen av HealthEvent-objekt som returneras baserat på hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden, som erhålls med hjälp av operatorn "ELLER". Om det angivna värdet till exempel är 6 returneras alla händelser med HealthState-värdet OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --exklude-hälso-statistik | Anger om hälsostatistiken ska returneras som en del av frågeresultatet. Falskt som standard. Statistiken visar antalet underordnade entiteter i hälsotillståndet Ok, Varning och Fel. |
| --include-system-application-health-statistics --include-system-application-health-statistics --include-system-application-health-statistics -- | Anger om hälsostatistiken ska innehålla\:hälsostatistiken för programmet /System application. Falskt som standard. Om IncludeSystemApplicationHealthStatistics är inställt på true, omfattar hälsostatistiken de\:enheter som tillhör fabric /System-programmet. Annars innehåller frågeresultatet endast hälsostatistik för användarprogram. Hälsostatistiken måste inkluderas i frågeresultatet för den här parametern som ska tillämpas. |
| --noder-hälsotillstånd-filter | Gör det möjligt att filtrera de nodhälsotillståndsobjekt som returneras i resultatet av klusterhälsofrågan baserat på deras hälsotillstånd. De möjliga värdena för den här parametern inkluderar heltalsvärdet för något av följande hälsotillstånd. Endast noder som matchar filtret returneras. Alla noder används för att utvärdera det aggregerade hälsotillståndet. Om inget anges returneras alla transaktioner. Tillståndsvärdena är flaggbaserad uppräkning, så värdet kan vara en kombination av dessa värden som erhålls med hjälp av bitvis "ELLER"-operator. Om värdet till exempel är 6 returneras hälsotillståndet för noder med HealthState-värde på OK (2) och Varning (4).  <br> - Standard - Standardvärde. Matchar alla HealthState. Värdet är noll.  <br> - Ingen - Filter som inte matchar något HealthState-värde. Används för att returnera inga resultat på en viss samling av stater. Värdet är 1.  <br> - Ok - Filter som matchar indata med HealthState-värdet Ok. Värdet är 2.  <br> - Varning - Filter som matchar indata med HealthState-värde Varning. Värdet är 4.  <br> - Fel - Filter som matchar indata med HealthState-värdefel. Värdet är 8.  <br> - Alla - Filter som matchar indata med alla HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-manifest"></a>sfctl kluster manifest
Hämta klustermanifestet För servicetyg.

Hämta klustermanifestet För servicetyg. Klustermanifestet innehåller egenskaper för klustret som innehåller olika nodtyper i klustret, säkerhetskonfigurationer, fel och uppgradera domäntopologier osv. Dessa egenskaper anges som en del av Filen ClusterConfig.JSON när du distribuerar ett fristående kluster. Men det mesta av informationen i klustermanifestet genereras internt av tjänstinfrastruktur under klusterdistribution i andra distributionsscenarier (t.ex. när du använder Azure-portalen). Innehållet i klustermanifestet är endast i informationssyfte och användarna förväntas inte vara beroende av formatet på filinnehållet eller dess tolkning.

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

## <a name="sfctl-cluster-operation-cancel"></a>sfctl-klusteråtgärdsstopp
Avbryter en felåtgärd som orsakas av användaren.

Följande API:er startar felåtgärder som kan\: avbrytas med hjälp av CancelOperation StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Om kraften är falsk stoppas och rensas den angivna användarinducerade åtgärden.  Om våld är sant avbryts kommandot och vissa interna tillstånd kan lämnas kvar.  Ange kraft som sant bör användas med försiktighet. Att anropa det här API:et med kraft inställt på true är inte tillåtet förrän det här API:et redan har anropats på samma testkommando med kraft inställd på false först, eller om inte testkommandot redan har en OperationState of OperationState.RollingBack. Förtydligande\: OperationState.RollingBack innebär att systemet kommer att /är att rensa upp interna systemtillstånd som orsakas av att köra kommandot.  Data återställs inte om testkommandot skulle orsaka dataförlust.  Om du till exempel anropar StartDataLoss och sedan anropar det här API:et rensar systemet bara upp internt tillstånd från att köra kommandot. Det återställer inte målpartitionens data om kommandot gick tillräckligt långt för att orsaka dataförlust. Viktigt\: att notera om detta API anropas med force==true, kan internt tillstånd lämnas kvar.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --operation-id [Obligatoriskt] | Ett GUID som identifierar ett anrop av det här API:et.  Detta skickas till motsvarande GetProgress API. |
| --kraft | Anger om du ska återställa och rensa internt systemtillstånd som ändrats genom att utföra den användarinducerade åtgärden. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-operation-list"></a>sfctl kluster funktion-lista
Hämtar en lista över användarinducerade felåtgärder filtrerade efter medföljande indata.

Hämtar listan över användarinducerade felåtgärder filtrerade efter medföljande indata.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --state-filter | Används för att filtrera på OperationState's för användarinducerade åtgärder. - 65535 - välj Alla - 1 - välj Running - 2 - välj RollingBack - 8 - välj Slutförd - 16 - välj Fel - 32 - välj Avbruten - 64 - välj ForceCancelled.  Standard\: 65535. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |
| --typ-filter | Används för att filtrera på OperationType för användarinducerade åtgärder. - 65535 - välj alla - 1 - välj PartitionDataLoss. - 2 - välj PartitionQuorumLoss. - 4 - välj PartitionRestart. - 8 - välj NodeTransition.  Standard\: 65535. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-provision"></a>sfctl klusteretablering
Etablera kod- eller konfigurationspaket för ett Service Fabric-kluster.

Validera och etablera kod- eller konfigurationspaketen för ett Service Fabric-kluster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --cluster-manifest-file-path --cluster-manifest-file-path --cluster-manifest-file-path -- | Sökvägen till klustermanifestfilen. |
| --kod-fil-sökväg | Sökvägen till filsökvägen för klusterkodpaketet. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-recover-system"></a>sfctl kluster recover-system
Anger för servicetygsklustret att det ska försöka återställa de systemtjänster som för närvarande har fastnat i kvorumförlust.

Anger för servicetygsklustret att det ska försöka återställa de systemtjänster som för närvarande har fastnat i kvorumförlust. Den här åtgärden bör endast utföras om det är känt att replikerna som är nere inte kan återställas. Felaktig användning av detta API kan orsaka potentiell dataförlust.

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

## <a name="sfctl-cluster-report-health"></a>sfctl kluster rapport-hälsa
Skickar en hälsorapport i service fabric-klustret.

Skickar en hälsorapport om ett Service Fabric-kluster. Rapporten måste innehålla information om källan till hälsorapporten och egenskapen som den rapporteras till. Rapporten skickas till en Gateway-nod för Service Fabric som vidarebefordras till hälsoarkivet. Rapporten kan accepteras av gatewayen, men avvisas av hälsoarkivet efter extra validering. Hälsoarkivet kan till exempel avvisa rapporten på grund av en ogiltig parameter, till exempel ett inaktuellt sekvensnummer. Om du vill se om rapporten har tillämpats i hälsoarkivet kör du GetClusterHealth och kontrollerar att rapporten visas i avsnittet HealthEvents.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --health-property [Obligatoriskt] | Egenskapen av hälsoinformationen. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning för att tillåta reporterflexibilitet att kategorisera tillståndsvillkoret som utlöser rapporten. En reporter med SourceId "LocalWatchdog" kan till exempel övervaka tillståndet för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på den noden. Samma reporter kan övervaka nodanslutningen, så att den kan rapportera en egenskap "Anslutning" på samma nod. I hälsoarkivet behandlas dessa rapporter som separata hälsohändelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen hälsoinformationen unikt. |
| --hälsotillstånd [Krävs] | Möjliga värden\: är "Ogiltig", "Ok", "Varning", "Fel", "Okänd". |
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

## <a name="sfctl-cluster-select"></a>sfctl kluster välj
Ansluter till en service fabric-klusterslutpunkt.

Om du ansluter till säkert kluster anger du en absolut sökväg till en cert (.crt) och nyckelfil (.key) eller en enda fil med båda (.pem). Ange inte båda. Om du ansluter till ett säkert kluster anger du också en absolut sökväg till en ca-paketfil eller katalog med betrodda CERTIFIKAT.  Det finns ingen anslutning till ett kluster utan att köra det här kommandot först, inklusive en anslutning till localhost. Ingen explicit slutpunkt krävs dock för att ansluta till ett lokalt kluster.  Om du använder ett självsignerat certifikat eller annat certifikat som inte är signerat av en välkänd certifikatutfärdar, skickar du in parametern --ca för att säkerställa att valideringen passerar. Om inte på ett produktionskluster, för att kringgå validering på klientsidan (användbart för självsignerad eller inte välkänd ca-signerad), använd alternativet --no-verify. Även om det är möjligt rekommenderas det inte för produktionskluster. Ett certifikatverifieringsfel kan uppstå på annat sätt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --aad | Använd Azure Active Directory för autentisering. |
| - Ca | Absolut sökväg till CA-certs-katalogen som ska behandlas som giltig eller CA-paketfil. Om du använder en katalog `c_rehash <directory>` med CA-certifikat måste den som tillhandahålls av OpenSSL köras först för att beräkna certifikatets hashar och skapa lämpliga symboliska länkar. Detta används för att kontrollera att certifikatet som returneras av klustret är giltigt. |
| --cert | Absolut sökväg till en klientcertifikatfil. |
| --slutpunkt | Url till klusterslutpunkt, inklusive port- och HTTP- eller HTTPS-prefix. Slutpunkten ser vanligtvis ut ungefär\:som https //<din webbadress \:>19080. Om ingen slutpunkt anges, kommer\:det att\:standard http //localhost 19080.  Standard\: \:http //localhost\:19080. |
| --nyckel | Absolut sökväg till klientcertifikatnyckelfil. |
| --ingen-verifiera | Inaktivera verifiering för certifikat när\: du använder HTTPS, observera att detta är ett osäkert alternativ och bör inte användas för produktionsmiljöer. |
| --pem | Absolut sökväg till klientcertifikat, som en PEM-fil. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-show-connection"></a>sfctl kluster show-anslutning
Visa vilket Service Fabric-kluster som den här sfctl-instansen är ansluten till.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-unprovision"></a>sfctl kluster avetablerande
Avetablera kod- eller konfigurationspaket för ett Service Fabric-kluster.

Det stöds för att avetablera kod och konfiguration separat.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kod-version | Klusterkodpaketversionen. |
| --config-version | Klustermanifestversionen. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-upgrade"></a>uppgradering av sfctl-kluster
Börja uppgradera koden eller konfigurationsversionen av ett Service Fabric-kluster.

Validera de medföljande uppgraderingsparametrarna och börja uppgradera koden eller konfigurationsversionen av ett Service Fabric-kluster om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-hälsa-karta | JSON kodade ordlista med par av programnamn och maximal procentandel fel innan du höjer fel. |
| --app-typ-hälsa-karta | JSON kodade ordlista med par av programtypsnamn och maximal procentandel som var felaktig innan felupptäckt. |
| --kod-version | Klusterkodsversionen. |
| --config-version | Klusterkonfigurationsversionen. |
| --delta-hälsa-utvärdering | Aktiverar delta hälsoutvärdering snarare än absolut hälsoutvärdering efter slutförandet av varje uppgraderingsdomän. |
| --delta-ohälsosam-noder | Den högsta tillåtna procentandelen av noder hälsoförsämring tillåts under klusteruppgraderingar.  Standard\: 10. <br><br> Deltat mäts mellan nodernas tillstånd i början av uppgraderingen och nodernas tillstånd vid tidpunkten för hälsoutvärderingen. Kontrollen utförs efter varje uppgradering domän uppgradering slutföras för att se till att det globala tillståndet för klustret är inom tolererade gränser. |
| --fel-åtgärd | Möjliga värden\: är "Ogiltig", "Återställning", "Manuell". |
| --force-restart | Processer startas om kraftfullt under uppgraderingen även när kodversionen inte har ändrats. <br><br> Uppgraderingen ändrar bara konfiguration eller data. |
| --hälsokontroll-nyttförsök | Hur lång tid det går mellan försök att utföra hälsokontroller om programmet eller klustret inte är felfritt. |
| --hälsokontroll-stabil | Den tid som programmet eller klustret måste förbli felfritt innan uppgraderingen fortsätter till nästa uppgraderingsdomän. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälsa-check-vänta | Hur lång tid du ska vänta efter att ha slutfört en uppgraderingsdomän innan hälsokontrollprocessen startas. |
| --replica-set-check-timeout | Den maximala tiden för att blockera bearbetning av en uppgraderingsdomän och förhindra förlust av tillgänglighet när det finns oväntade problem. <br><br> När den här timeouten går ut fortsätter bearbetningen av uppgraderingsdomänen oavsett problem med tillgänglighetsförlust. Tidsgränsen återställs i början av varje uppgraderingsdomän. Giltiga värden är mellan 0 och 42949672925. |
| --rolling-upgrade-mode | Möjliga värden\: är "Ogiltig", "UnmonitoredAuto", "UnmonitoredManual", "Övervakad".  Standard\: unmonitoredauto. |
| --timeout -t | Standard\: 60. |
| --ohälsosamma-applikationer | Den högsta tillåtna procentandelen felaktiga program innan du rapporterar ett fel. <br><br> Om du till exempel vill att 10 % av programmen ska vara felaktiga, skulle det här värdet vara 10. Procentsatsen representerar den maximala tolererade procentandelen av program som kan vara felaktiga innan klustret betraktas som ett fel. Om procentsatsen respekteras men det finns minst ett felaktigt program utvärderas hälsotillståndet som Varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet programinstanser i klustret, exklusive program av programtyper som ingår i ApplicationTypeHealthPolicyMap. Uträkningen avrundar upp till tolerera ett fel på litet nummer av applikationer. |
| --ohälsosam-noder | Den högsta tillåtna procentandelen felaktiga noder innan du rapporterar ett fel. <br><br> Om du till exempel vill att 10 % av noderna ska vara felaktiga, skulle det här värdet vara 10. Procentsatsen representerar den maximala tolererade procentandelen noder som kan vara felaktiga innan klustret betraktas som ett fel. Om procentsatsen respekteras men det finns minst en felaktig nod utvärderas hälsotillståndet som Varning. Procentsatsen beräknas genom att dividera antalet felaktiga noder över det totala antalet noder i klustret. Beräkningen avrundar upp för att tolerera ett fel på ett litet antal noder. I stora kluster kommer vissa noder alltid att vara nere eller ute för reparationer, så den här procentsatsen bör konfigureras för att tolerera det. |
| --upgrade-domain-delta-unhealthy-noder | Den högsta tillåtna procentandelen uppgraderingsdomännoder hälsoförsämring som tillåts vid klusteruppgraderingar.  Standard\: 15. <br><br> Deltat mäts mellan tillståndet för uppgraderingsdomännoderna i början av uppgraderingen och tillståndet för uppgraderingsdomännoderna vid tidpunkten för hälsoutvärderingen. Kontrollen utförs efter varje uppgradering domän uppgradering slutförande för alla slutförda uppgradera domäner för att se till att tillståndet för uppgraderingsdomäner är inom tolererade gränser. |
| --upgrade-domain-timeout --upgrade-domain-timeout --upgrade-domain-timeout -- | Den tid som varje uppgraderingsdomän måste slutföra innan FailureAction körs. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --upgrade-timeout --upgrade-timeout --upgrade-timeout -- | Hur lång tid den totala uppgraderingen måste slutföras innan FailureAction körs. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --varning-som-fel | Anger om varningar behandlas med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl kluster uppgradering-återuppta
Gör klusteruppgraderingen gå vidare till nästa uppgraderingsdomän.

Gör klusterkoden eller konfigurationsuppgraderingen vidare till nästa uppgraderingsdomän om det behövs.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --upgrade-domain [Obligatoriskt] | Nästa uppgraderingsdomän för den här klusteruppgraderingen. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl kluster uppgradering-återställning
Återställ uppgraderingen av ett Service Fabric-kluster.

Återställ koden eller konfigurationsuppgraderingen för ett Service Fabric-kluster.

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

## <a name="sfctl-cluster-upgrade-status"></a>sfctl kluster uppgradering-status
Hämtar förloppet för den aktuella klusteruppgraderingen.

Hämtar den aktuella förloppet för den pågående klusteruppgraderingen. Om ingen uppgradering pågår får du det sista tillståndet för den tidigare klusteruppgraderingen.

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

## <a name="sfctl-cluster-upgrade-update"></a>uppgraderingsuppdatering för sfctl-kluster
Uppdatera uppgraderingsparametrarna för en uppgradering av Service Fabric-kluster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-hälsa-karta | JSON kodade ordlista med par av programnamn och maximal procentandel fel innan du höjer fel. |
| --app-typ-hälsa-karta | JSON kodade ordlista med par av programtypsnamn och maximal procentandel som var felaktig innan felupptäckt. |
| --delta-hälsa-utvärdering | Aktiverar delta hälsoutvärdering snarare än absolut hälsoutvärdering efter slutförandet av varje uppgraderingsdomän. |
| --delta-ohälsosam-noder | Den högsta tillåtna procentandelen av noder hälsoförsämring tillåts under klusteruppgraderingar.  Standard\: 10. <br><br> Deltat mäts mellan nodernas tillstånd i början av uppgraderingen och nodernas tillstånd vid tidpunkten för hälsoutvärderingen. Kontrollen utförs efter varje uppgradering domän uppgradering slutföras för att se till att det globala tillståndet för klustret är inom tolererade gränser. |
| --fel-åtgärd | Möjliga värden\: är "Ogiltig", "Återställning", "Manuell". |
| --force-restart | Processer startas om kraftfullt under uppgraderingen även när kodversionen inte har ändrats. <br><br> Uppgraderingen ändrar bara konfiguration eller data. |
| --hälsokontroll-nyttförsök | Hur lång tid det går mellan försök att utföra hälsokontroller om programmet eller klustret inte är felfritt. |
| --hälsokontroll-stabil | Den tid som programmet eller klustret måste förbli felfritt innan uppgraderingen fortsätter till nästa uppgraderingsdomän. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälsa-check-vänta | Hur lång tid du ska vänta efter att ha slutfört en uppgraderingsdomän innan hälsokontrollprocessen startas. |
| --replica-set-check-timeout | Den maximala tiden för att blockera bearbetning av en uppgraderingsdomän och förhindra förlust av tillgänglighet när det finns oväntade problem. <br><br> När den här timeouten går ut fortsätter bearbetningen av uppgraderingsdomänen oavsett problem med tillgänglighetsförlust. Tidsgränsen återställs i början av varje uppgraderingsdomän. Giltiga värden är mellan 0 och 42949672925. |
| --rolling-upgrade-mode | Möjliga värden\: är "Ogiltig", "UnmonitoredAuto", "UnmonitoredManual", "Övervakad".  Standard\: unmonitoredauto. |
| --timeout -t | Standard\: 60. |
| --ohälsosamma-applikationer | Den högsta tillåtna procentandelen felaktiga program innan du rapporterar ett fel. <br><br> Om du till exempel vill att 10 % av programmen ska vara felaktiga, skulle det här värdet vara 10. Procentsatsen representerar den maximala tolererade procentandelen av program som kan vara felaktiga innan klustret betraktas som ett fel. Om procentsatsen respekteras men det finns minst ett felaktigt program utvärderas hälsotillståndet som Varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet programinstanser i klustret, exklusive program av programtyper som ingår i ApplicationTypeHealthPolicyMap. Uträkningen avrundar upp till tolerera ett fel på litet nummer av applikationer. |
| --ohälsosam-noder | Den högsta tillåtna procentandelen felaktiga noder innan du rapporterar ett fel. <br><br> Om du till exempel vill att 10 % av noderna ska vara felaktiga, skulle det här värdet vara 10. Procentsatsen representerar den maximala tolererade procentandelen noder som kan vara felaktiga innan klustret betraktas som ett fel. Om procentsatsen respekteras men det finns minst en felaktig nod utvärderas hälsotillståndet som Varning. Procentsatsen beräknas genom att dividera antalet felaktiga noder över det totala antalet noder i klustret. Beräkningen avrundar upp för att tolerera ett fel på ett litet antal noder. I stora kluster kommer vissa noder alltid att vara nere eller ute för reparationer, så den här procentsatsen bör konfigureras för att tolerera det. |
| --upgrade-domain-delta-unhealthy-noder | Den högsta tillåtna procentandelen uppgraderingsdomännoder hälsoförsämring som tillåts vid klusteruppgraderingar.  Standard\: 15. <br><br> Deltat mäts mellan tillståndet för uppgraderingsdomännoderna i början av uppgraderingen och tillståndet för uppgraderingsdomännoderna vid tidpunkten för hälsoutvärderingen. Kontrollen utförs efter varje uppgradering domän uppgradering slutförande för alla slutförda uppgradera domäner för att se till att tillståndet för uppgraderingsdomäner är inom tolererade gränser. |
| --upgrade-domain-timeout --upgrade-domain-timeout --upgrade-domain-timeout -- | Den tid som varje uppgraderingsdomän måste slutföra innan FailureAction körs. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --upgrade-kind | Möjliga värden\: är "Ogiltig", "Rullande", "Rolling_ForceRestart".  Standardrullning.\: |
| --upgrade-timeout --upgrade-timeout --upgrade-timeout -- | Hur lång tid den totala uppgraderingen måste slutföras innan FailureAction körs. <br><br> Det tolkas först som en sträng som representerar en ISO 8601 varaktighet. Om det misslyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --varning-som-fel | Anger om varningar behandlas med samma allvarlighetsgrad som fel. |

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