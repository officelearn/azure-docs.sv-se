---
title: Azure Service Fabric CLI och sfctl repliken | Microsoft Docs
description: Beskriver sfctl replik-kommandon för Service Fabric CLI.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: d0a7199ff0e9cb17c3fbc179a9b37a6620f521f9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666825"
---
# <a name="sfctl-replica"></a>sfctl replica
Hantera replikerna som hör till tjänstpartitioner.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| distribuerat | Hämtar information om repliken som distribuerats på en Service Fabric-nod. |
| distribuerat lista | Hämtar listan över kopior som distribueras på en Service Fabric-nod. |
| hälsa | Hämtar hälsotillståndet för en Service Fabric tillståndskänslig tjänst målreplik eller tillståndslös tjänst. |
| info | Hämtar information om en replik av en Service Fabric-partition. |
| lista | Hämtar information om repliker för en partition för Service Fabric-tjänsten. |
| ta bort | Tar bort en replik för tjänsten som körs på en nod. |
| rapportera hälsa | Skickar en hälsorapport på Service Fabric-replik. |
| restart | Startar om en tjänst replik av en bestående tjänst som körs på en nod. |

## <a name="sfctl-replica-deployed"></a>sfctl repliken distribueras
Hämtar information om repliken som distribuerats på en Service Fabric-nod.

Hämtar information om repliken som distribuerats på en Service Fabric-nod. Informationen omfattar tjänsttyp, tjänstnamn, aktuella tjänståtgärden, aktuella tjänståtgärden starta datum tid, partitions-ID, repliken/instans-ID, rapporterade belastning och annan information.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| --partition-id [Required] | Identiteten för partitionen. |
| --replik-id [krävs] | Identifierare för repliken. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-replica-deployed-list"></a>sfctl distribueras replica-list
Hämtar listan över kopior som distribueras på en Service Fabric-nod.

Hämtar listan som innehåller information om kopior som distribueras på en Service Fabric-nod. Informationen omfattar partitions-ID, replik-ID, status för repliken, namnet på tjänsten, namnet på tjänsttypen och annan information. Använd PartitionId eller ServiceManifestName frågeparametrar för att returnera information om distribuerade repliker som matchar de angivna värdena för dessa parametrar.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --application-id [Required] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan att den ”fabric\:” URI-schema. Från och med version 6.0, hierarkiska namn avgränsas med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --partition-id | Identiteten för partitionen. |
| --service-manifest-name | Namnet på ett tjänstmanifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-replica-health"></a>sfctl replica health
Hämtar hälsotillståndet för en Service Fabric tillståndskänslig tjänst målreplik eller tillståndslös tjänst.

Hämtar hälsotillståndet för en Service Fabric-replik. Använd EventsHealthStateFilter för att filtrera insamling av health-händelser som rapporteras på repliken baserat på hälsotillståndet.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identiteten för partitionen. |
| --replik-id [krävs] | Identifierare för repliken. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-replica-info"></a>sfctl repliken info
Hämtar information om en replik av en Service Fabric-partition.

Svaret innehåller ID: T, roll, status, hälsotillstånd, nodnamnet, drifttid och annan information om repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Required] | Identiteten för partitionen. |
| --replik-id [krävs] | Identifierare för repliken. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-replica-list"></a>sfctl replikeringslistan
Hämtar information om repliker för en partition för Service Fabric-tjänsten.

GetReplicas slutpunkten returnerar information om replikerna för den angivna partitionen. Svaret innehåller ID: T, roll, status, hälsotillstånd, nodnamnet, drifttid och annan information om repliken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --partition-id [Required] | Identiteten för partitionen. |
| --continuation-token | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-replica-remove"></a>ta bort repliken av sfctl
Tar bort en replik för tjänsten som körs på en nod.

Detta API simulerar ett fel för Service Fabric-replik genom att ta bort en replik från ett Service Fabric-kluster. Borttagningen stängs repliken, övergår replikeringen till rollen ingen och sedan tar bort alla av tillståndsinformationen av repliken från klustret. Detta API testar replikeringssökvägen tillstånd borttagning och simulerar fel permanent rapportsökvägen via klientens API: er. Varning - det finns finns ingen säkerhet kontroll som utförs när detta API används. Felaktig användning av detta API kan leda till förlust av data för tillståndskänsliga tjänster. Dessutom påverkar flaggan forceRemove alla repliker på samma process.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| --partition-id [Required] | Identiteten för partitionen. |
| --replik-id [krävs] | Identifierare för repliken. |
| --force-remove | Ta bort en Service Fabric-program eller tjänst kernelpaketet utan att gå igenom de avslutning. Den här parametern kan användas för att kernelpaketet ta bort ett program eller tjänst för vilka borttagning är avbryts på grund av problem i den kod som förhindrar korrekt slutet av repliker. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-replica-report-health"></a>sfctl replica report-health
Skickar en hälsorapport på Service Fabric-replik.

Rapporterar hälsotillståndet för den angivna Service Fabric-repliken. Rapporten måste innehålla information om orsaken hälsorapport och egenskapen som har rapporterats. Rapporten skickas till en Service Fabric gateway repliken som vidarebefordrar till health store. Rapporten kan accepteras av gatewayen, men avvisats av health store efter extra valideringen. Health store kan till exempel avvisa rapporten på grund av en ogiltig parameter, t.ex. ett sekvensnummer som är inaktuella. Om du vill se om rapporten har tillämpats i health store, få kör repliken hälso- och kontrollera att rapporten visas i avsnittet HealthEvents.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --health-egenskapen [krävs] | Egenskapen för hälsoinformation. <br><br> En entitet kan ha rapporter om hälsotillstånd för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport flexibiliteten att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan till exempel övervaka status för tillgängliga disken på en nod, så att den kan rapportera ”AvailableDisk”-egenskap på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. Dessa rapporter behandlas i health store, som separata health-händelser för den angivna noden. Tillsammans med målentiteten identifierar egenskapen hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden omfattar\: ”ogiltig”, ”Ok”, ”varning”, ”fel”, ”okänd”. |
| --partitions-id [krävs] | Identiteten för partitionen. |
| --replik-id [krävs] | Identiteten för partitionen. |
| --käll-id [krävs] | Källnamn som identifierar/klientsystemet/watchdog-komponenten som genererade hälsoinformation. |
| – Beskrivning | Beskrivning av hälsoinformation. <br><br> Den motsvarar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4 096 tecken. Om strängen inte trunkeras den automatiskt. När trunkeras, de sista tecknen i beskrivningen innehåller en markör ”[trunkerat]” och totala storleken på målsträngen är 4 096 tecken. Förekomst av markören anger för användare att trunkering inträffade. Observera att när trunkeras, beskrivningen har mindre än 4 096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till en Service Fabric gateway programmet, som vidarebefordrar till health store. Om Immediate anges till SANT, skickas rapporten direkt från HTTP-Gateway till health store, oavsett inställningarna i fabric-klient som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas skicka rapporten fortfarande, till exempel om HTTP-Gateway är stängd eller meddelandet når inte gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningarna hälsotillstånd från HTTP-Gateway. Det kan därför batchhanteras enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom den tillåter hälsotillstånd klienten att optimera reporting meddelanden hälsoarkivet, samt för bearbetning av hälsotillstånd. Som standard skickas rapporter inte omedelbart. |
| --remove-when-expired | Värde som anger om rapporten tas bort från health store när den upphör att gälla. <br><br> Om värdet är true, rapporten tas bort från health store när den upphör att gälla. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). På så sätt kan är personen har problem (t.ex. deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
| ---sekvensnummer | Sekvensnumret för den här hälsorapport som en numerisk sträng. <br><br> Sekvensnumret rapporten används av health store för att identifiera inaktuella rapporter. Om inte anges genereras ett sekvensnummer automatiskt av hälsotillstånd klienten när du lägger till en rapport. |
| – typen av tjänst | Vilken typ av tjänsterepliken (tillståndslösa eller tillståndskänsliga) som hälsotillstånd rapporteras. Följande är möjliga värden\: 'Tillståndslösa', 'Tillståndskänslig'.  Standard\: tillståndskänslig. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --ttl | Den tid som den här hälsorapport är giltig. Det här fältet använder ISO8601-format för att ange varaktigheten. <br><br> När klienterna rapporterar med jämna mellanrum, bör de skicka rapporter med frekvens som är högre än TTL-värde. Om klienterna rapporterar på övergång, kan de ange time to live till obegränsad. När TTL-värde upphör att gälla hälsohändelsen som innehåller hälsoinformation är antingen tas bort från health store om RemoveWhenExpired är true och utvärderas vid fel, om RemoveWhenExpired false. Om inte tidpunkten TTL-värde standardvärdet är oändligt värde. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-replica-restart"></a>sfctl repliken omstart
Startar om en tjänst replik av en bestående tjänst som körs på en nod.

Startar om en tjänst replik av en bestående tjänst som körs på en nod. Varning - det finns finns ingen säkerhet kontroll som utförs när detta API används. Felaktig användning av detta API kan leda till förlust av tillgänglighet för tillståndskänsliga tjänster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| --partition-id [Required] | Identiteten för partitionen. |
| --replik-id [krävs] | Identifierare för repliken. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |


## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
