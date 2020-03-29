---
title: AZURE Service Fabric CLI- sfctl-replik
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för att hantera repliker.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f6ad0b4c08ac8d710340fe654a068d0a3804e58f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905807"
---
# <a name="sfctl-replica"></a>sfctl replica
Hantera replikerna som tillhör tjänstpartitioner.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Distribuerat | Hämtar information om replik som distribueras på en service fabric-nod. |
| distribuerad lista | Hämtar listan över repliker som distribueras på en service fabric-nod. |
| Hälsa | Hämtar hälsotillstånd för en tillståndskänslig tjänstreplik för tjänst för tjänst eller tillståndslös tjänstinstans. |
| information | Hämtar information om en replik av en Service Fabric-partition. |
| lista | Hämtar information om repliker av en tjänstpartition för Tjänst Fabric. |
| Ta bort | Tar bort en tjänstreplik som körs på en nod. |
| rapport-hälsa | Skickar en hälsorapport på repliken Service Fabric. |
| restart | Startar om en tjänstreplik av en beständig tjänst som körs på en nod. |

## <a name="sfctl-replica-deployed"></a>sfctl-replik distribuerad
Hämtar information om replik som distribueras på en service fabric-nod.

Hämtar information om repliken som distribueras på en service fabric-nod. Informationen omfattar tjänststund, tjänstnamn, aktuell tjänståtgärd, aktuell tjänståtgärdsstartdatumtid, partitions-ID, replik-/instans-ID, rapporterad inläsning och annan information.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --replica-id [Obligatoriskt] | Identifieraren för repliken. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-replica-deployed-list"></a>sfctl-replik distribuerad lista
Hämtar listan över repliker som distribueras på en service fabric-nod.

Hämtar listan som innehåller information om repliker som distribueras på en service fabric-nod. Informationen omfattar partitions-ID, replik-ID, replikens status, tjänstens namn, tjänsttypens namn och annan information. Använd PartitionId- eller ServiceManifestName-frågeparametrar för att returnera information om de distribuerade replikerna som matchar de angivna värdena för dessa parametrar.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Obligatoriskt] | Programmets identitet. Detta är vanligtvis det fullständiga namnet på\:programmet utan "fabric" URI-schemat. Från och med version 6.0 avgränsas hierarkiska\~namn med " " tecken. Om programnamnet till exempel är\:"fabric /myapp/app1" skulle programidentiteten vara "myapp\~app1" i 6.0+ och "myapp/app1" i tidigare versioner. |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --partition-id | Partitionens identitet. |
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

## <a name="sfctl-replica-health"></a>sfctl replika hälsa
Hämtar hälsotillstånd för en tillståndskänslig tjänstreplik för tjänst för tjänst eller tillståndslös tjänstinstans.

Hämtar hälsotillståndet för en Service Fabric-replik. Använd EventsHealthStateFilter för att filtrera insamlingen av hälsohändelser som rapporterats på repliken baserat på hälsotillståndet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --replica-id [Obligatoriskt] | Identifieraren för repliken. |
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

## <a name="sfctl-replica-info"></a>sfctl replika info
Hämtar information om en replik av en Service Fabric-partition.

Svaret innehåller ID, roll, status, hälsa, nodnamn, drifttid och annan information om repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --replica-id [Obligatoriskt] | Identifieraren för repliken. |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-replica-list"></a>sfctl-repliklista
Hämtar information om repliker av en tjänstpartition för Tjänst Fabric.

GetReplicas-slutpunkten returnerar information om replikerna för den angivna partitionen. Svaret innehåller ID, roll, status, hälsa, nodnamn, drifttid och annan information om repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
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

## <a name="sfctl-replica-remove"></a>sfctl replika ta bort
Tar bort en tjänstreplik som körs på en nod.

Det här API:et simulerar ett e-postkontrollfel genom att ta bort en replik från ett Service Fabric-kluster. Borttagningen stänger repliken, överför repliken till rollen Ingen och tar sedan bort all tillståndsinformation för repliken från klustret. Det här API:et testar sökvägen för borttagning av repliktillstånd och simulerar den permanenta sökvägen för rapportfelet via klient-API:er. Varning - Det finns inga säkerhetskontroller som utförs när detta API används. Felaktig användning av det här API:et kan leda till dataförlust för tillståndskänsliga tjänster. Dessutom påverkar forceRemove-flaggan alla andra repliker som finns i samma process.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --replica-id [Obligatoriskt] | Identifieraren för repliken. |
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

## <a name="sfctl-replica-report-health"></a>sfctl replika rapport-hälsa
Skickar en hälsorapport på repliken Service Fabric.

Rapporterar hälsotillståndet för den angivna service fabric-repliken. Rapporten måste innehålla information om källan till hälsorapporten och egenskapen som den rapporteras till. Rapporten skickas till en Service Fabric gateway Replica, som vidarebefordras till hälsoarkivet. Rapporten kan accepteras av gatewayen, men avvisas av hälsoarkivet efter extra validering. Hälsoarkivet kan till exempel avvisa rapporten på grund av en ogiltig parameter, till exempel ett inaktuellt sekvensnummer. Om du vill se om rapporten har tillämpats i hälsoarkivet kör du replikhälsa och kontrollerar att rapporten visas i avsnittet HealthEvents.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --health-property [Obligatoriskt] | Egenskapen av hälsoinformationen. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning för att tillåta reporterflexibilitet att kategorisera tillståndsvillkoret som utlöser rapporten. En reporter med SourceId "LocalWatchdog" kan till exempel övervaka tillståndet för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på den noden. Samma reporter kan övervaka nodanslutningen, så att den kan rapportera en egenskap "Anslutning" på samma nod. I hälsoarkivet behandlas dessa rapporter som separata hälsohändelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen hälsoinformationen unikt. |
| --hälsotillstånd [Krävs] | Möjliga värden\: är "Ogiltig", "Ok", "Varning", "Fel", "Okänd". |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --replica-id [Obligatoriskt] | Partitionens identitet. |
| --source-id [Obligatoriskt] | Källnamnet som identifierar komponenten klient/watchdog/system som genererade hälsoinformationen. |
| --beskrivning | Beskrivningen av hälsoinformationen. <br><br> Den representerar fritext som används för att lägga till läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4096 tecken. Om den angivna strängen är längre trunkeras den automatiskt. När de sista tecknen i beskrivningen trunkeras innehåller de sista tecknen i beskrivningen en markör "[Trunkerad]" och den totala strängstorleken 4096 tecken. Förekomsten av markören anger för användarna att trunkering inträffade. Observera att när beskrivningen trunkeras har den mindre än 4096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till ett service fabric-gatewayprogram som vidarebefordras till hälsoarkivet. Om Omedelbar är inställd på true skickas rapporten omedelbart från HTTP Gateway till hälsoarkivet, oavsett de inställningar för fabric-klient som HTTP Gateway-programmet använder. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor kan det fortfarande misslyckas att skicka rapporten, till exempel om HTTP-gatewayen är stängd eller om meddelandet inte når gatewayen. Om Omedelbar är inställd på false skickas rapporten baserat på hälsoklientinställningarna från HTTP Gateway. Därför kommer den att batchas enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom hälsoklienten kan optimera hälsorapporteringsmeddelanden till hälsoarkivet samt bearbetning av hälsorapporter. Som standard skickas inte rapporter omedelbart. |
| --remove-when-expired --remove-when-expired --remove-when-expired -- | Värde som anger om rapporten tas bort från hälsoarkivet när den upphör att gälla. <br><br> Om värdet är true tas rapporten bort från hälsoarkivet när den har upphört att gälla. Om den är inställd på false behandlas rapporten som ett fel när den har upphört att gälla. Värdet för den här egenskapen är falskt som standard. När klienter rapporterar regelbundet bör de ange RemoveWhenExpired false (standard). På så sätt har reportern problem (t.ex. dödläge) och kan inte rapportera, entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som i felhälsotillstånd. |
| --sekvens-nummer | Sekvensnumret för den här hälsorapporten som en numerisk sträng. <br><br> Rapportsekvensnumret används av hälsoarkivet för att identifiera inaktuella rapporter. Om inget anges genereras ett sekvensnummer automatiskt av hälsoklienten när en rapport läggs till. |
| --service-typ | Den typ av tjänst replik (tillståndslös eller tillståndskänslig) som hälsan rapporteras. Följande är de\: möjliga värdena "statslösa", "statskänsliga".  Standardtillståndsfull.\: |
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

## <a name="sfctl-replica-restart"></a>sfctl replika starta om
Startar om en tjänstreplik av en beständig tjänst som körs på en nod.

Startar om en tjänstreplik av en beständig tjänst som körs på en nod. Varning - Det finns inga säkerhetskontroller som utförs när detta API används. Felaktig användning av det här API:et kan leda till tillgänglighetsförlust för tillståndskänsliga tjänster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --nodnamn [Obligatoriskt] | Namnet på noden. |
| --partition-id [Obligatoriskt] | Partitionens identitet. |
| --replica-id [Obligatoriskt] | Identifieraren för repliken. |
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
