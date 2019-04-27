---
title: Azure Service Fabric CLI - sfctl-nod | Microsoft Docs
description: Beskriver sfctl nod-kommandon för Service Fabric CLI.
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
ms.openlocfilehash: 08ea0081c84ea31b2b71d03679b1b527cf94c075
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556788"
---
# <a name="sfctl-node"></a>sfctl node
Hantera de noder som formar ett kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| inaktivera | Inaktivera en Service Fabric-klusternod med angivna inaktivering avsikt. |
| aktivera | Aktivera en Service Fabric-klusternod som för närvarande har inaktiverats. |
| hälsa | Hämtar hälsotillståndet för en Service Fabric-nod. |
| info | Hämtar information om en viss nod i Service Fabric-klustret. |
| lista | Hämtar listan över noder i Service Fabric-klustret. |
| läsa in | Hämtar load-information för en Service Fabric-nod. |
| remove-state | Meddelar Service Fabric att det beständiga tillståndet på en nod har permanent bort eller tappas bort. |
| rapportera hälsa | Skickar en hälsorapport på Service Fabric-noden. |
| restart | Startar om en Service Fabric-klusternod. |
| övergång | Startar eller stoppar en klusternod. |
| transition-status | Hämtar förloppet för en åtgärd som är igång med StartNodeTransition. |

## <a name="sfctl-node-disable"></a>sfctl noden inaktivera
Inaktivera en Service Fabric-klusternod med angivna inaktivering avsikt.

Inaktivera en Service Fabric-klusternod med angivna inaktivering avsikt. När inaktiveringen pågår, inaktivering avsikten kan ökade men inte minskas (till exempel en nod som har inaktiverats med en paus avsikt kan inaktiveras ytterligare med omstart, men inte tvärtom. Noder kan återaktiveras med aktivera nodåtgärden efter att de inaktiveras. Om inaktiveringen inte har slutförts, avbryts inaktiveringen. En nod som stängs av och startas igen när inaktiveras måste återaktiveras innan tjänster kommer att placeras på noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| --deactivation-intent | Beskriver syfte eller orsak till att inaktivera noden. Möjliga värden följande. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-node-enable"></a>sfctl noden aktivera
Aktivera en Service Fabric-klusternod som för närvarande har inaktiverats.

Aktiverar en Service Fabric-klusternod som för närvarande har inaktiverats. När aktiverat kan noden igen blir ett genomförbart mål för att placera nya repliker och alla inaktiverade repliker som är kvar på noden ska återaktiveras.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-node-health"></a>sfctl nodhälsan
Hämtar hälsotillståndet för en Service Fabric-nod.

Hämtar hälsotillståndet för en Service Fabric-nod. Använd EventsHealthStateFilter för att filtrera insamling av health-händelser som rapporteras på den nod som baseras på hälsotillståndet. Om noden som du anger med namnet inte finns i health store, returnerar det här ett fel.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
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

## <a name="sfctl-node-info"></a>sfctl noden info
Hämtar information om en viss nod i Service Fabric-klustret.

Svaret innehåller namn, status, ID, hälsa, drifttid och annan information om noden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-node-list"></a>sfctl nodlistan
Hämtar listan över noder i Service Fabric-klustret.

Svaret innehåller namn, status, ID, hälsa, drifttid och annan information om noderna.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --continuation-token | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| --max-results | Det maximala antalet resultat som ska returneras som en del av de växlade frågorna. Den här parametern definierar den övre gränsen för antalet resultat som returneras. Resultatet som returneras kan mindre än de angivna maximalt antal resultat om de inte passar i meddelandet enligt storleksbegränsningar max meddelande definieras i konfigurationen. Om den här parametern är noll eller inte har angetts, inkluderar växlade frågan så många resultat som möjligt som passar in i svarsmeddelandet. |
| --node-status-filter | Tillåter filtrering baserat på NodeStatus noderna. Endast de noder som kommer att matcha det angivna filtervärdet returneras. Filtervärdet kan vara något av följande.  Standard\: standard. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-node-load"></a>sfctl node load
Hämtar load-information för en Service Fabric-nod.

Hämtar information för belastning för en Service Fabric-nod för alla mått som har belastning eller nodkapaciteten som definierats.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-node-remove-state"></a>sfctl noden ta bort tillstånd
Meddelar Service Fabric att det beständiga tillståndet på en nod har permanent bort eller tappas bort.

Detta innebär att det inte går att återställa det sparade tillståndet för noden. Detta inträffar vanligtvis om en hårddisk har rensats ren, eller om en hårddisk kraschar. Noden har inte är tillgänglig för den här åtgärden ska lyckas. Den här åtgärden kan Service Fabric vet att replikerna på noden finns inte längre och som Service Fabric ska avbrytas väntar på dessa replikerna är igång. Kör inte denna cmdlet om tillståndet på noden inte har tagits bort och noden kan gå tillbaka med statusen intakta.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-node-report-health"></a>sfctl node report-health
Skickar en hälsorapport på Service Fabric-noden.

Rapporterar hälsotillståndet för den angivna Service Fabric-noden. Rapporten måste innehålla information om orsaken hälsorapport och egenskapen som har rapporterats. Rapporten skickas till ett Service Fabric gateway-noden, som vidarebefordrar till health store. Rapporten kan accepteras av gatewayen, men avvisats av health store efter extra valideringen. Health store kan till exempel avvisa rapporten på grund av en ogiltig parameter, t.ex. ett sekvensnummer som är inaktuella. Kontrollera att rapporten ska visas i avsnittet HealthEvents för att se om rapporten har tillämpats i health store.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --health-egenskapen [krävs] | Egenskapen för hälsoinformation. <br><br> En entitet kan ha rapporter om hälsotillstånd för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport flexibiliteten att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan till exempel övervaka status för tillgängliga disken på en nod, så att den kan rapportera ”AvailableDisk”-egenskap på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. Dessa rapporter behandlas i health store, som separata health-händelser för den angivna noden. Tillsammans med målentiteten identifierar egenskapen hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden omfattar\: ”ogiltig”, ”Ok”, ”varning”, ”fel”, ”okänd”. |
| --Nodnamnet [krävs] | Namnet på noden. |
| --käll-id [krävs] | Källnamn som identifierar/klientsystemet/watchdog-komponenten som genererade hälsoinformation. |
| – Beskrivning | Beskrivning av hälsoinformation. <br><br> Den motsvarar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4 096 tecken. Om strängen inte trunkeras den automatiskt. När trunkeras, de sista tecknen i beskrivningen innehåller en markör ”[trunkerat]” och totala storleken på målsträngen är 4 096 tecken. Förekomst av markören anger för användare att trunkering inträffade. Observera att när trunkeras, beskrivningen har mindre än 4 096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till en Service Fabric gateway programmet, som vidarebefordrar till health store. Om Immediate anges till SANT, skickas rapporten direkt från HTTP-Gateway till health store, oavsett inställningarna i fabric-klient som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas skicka rapporten fortfarande, till exempel om HTTP-Gateway är stängd eller meddelandet når inte gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningarna hälsotillstånd från HTTP-Gateway. Det kan därför batchhanteras enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom den tillåter hälsotillstånd klienten att optimera reporting meddelanden hälsoarkivet, samt för bearbetning av hälsotillstånd. Som standard skickas rapporter inte omedelbart. |
| --remove-when-expired | Värde som anger om rapporten tas bort från health store när den upphör att gälla. <br><br> Om värdet är true, rapporten tas bort från health store när den upphör att gälla. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). På så sätt kan är personen har problem (t.ex. deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
| ---sekvensnummer | Sekvensnumret för den här hälsorapport som en numerisk sträng. <br><br> Sekvensnumret rapporten används av health store för att identifiera inaktuella rapporter. Om inte anges genereras ett sekvensnummer automatiskt av hälsotillstånd klienten när du lägger till en rapport. |
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

## <a name="sfctl-node-restart"></a>sfctl nodomstart
Startar om en Service Fabric-klusternod.

Startar om en Service Fabric-klusternod som har redan startats.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| – Skapa-fabric-kraschdump | Välj True om du vill skapa en dump av processen för fabric-noden. Värdet är skiftlägeskänsligt.  Standard\: FALSKT. |
| – nod-instans-id | Instans-ID för målnoden. Om instans-ID har angetts noden startas om du bara med den aktuella instansen av noden. Ett standardvärde på ”0” matchar valfri instans-ID. Instans-ID kan hämtas med hjälp av get-noden frågan.  Standard\: 0. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-node-transition"></a>sfctl för nodövergång
Startar eller stoppar en klusternod.

Startar eller stoppar en klusternod.  En nod är en process, inte själva instansen OS.  Om du vill starta en nod, skicka ”Start” för parametern NodeTransitionType. Om du vill stoppa en nod, skicka ”stoppa” för parametern NodeTransitionType. Detta API startar åtgärden - när API: et returnerar inte kanske har slutförts noden övergår ännu. Anropa GetNodeTransitionProgress med samma OperationId att hämta förloppet för åtgärden.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| – nod-instans-id [krävs] | Instans-ID för noden för målnoden. Du kan kontrollera detta via GetNodeInfo API. |
| --Nodnamnet [krävs] | Namnet på noden. |
| ---övergång-nodtyp [krävs] | Anger vilken typ av övergången till att utföra.  NodeTransitionType.Start startar en stoppad nod. NodeTransitionType.Stop slutar att en nod som är igång. |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta skickas vidare till motsvarande GetProgress API. |
| --stop-duration-in-seconds [Required] | Varaktighet i sekunder, att hålla noden stoppats.  Det lägsta värdet är 600, Max är 14400.  När den här tiden har gått ut, kommer automatiskt noden tillbaka. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-node-transition-status"></a>sfctl övergången-nodstatus
Hämtar förloppet för en åtgärd som är igång med StartNodeTransition.

Hämtar förloppet för en åtgärd som utgick StartNodeTransition med hjälp av det tillhandahållna åtgärds-ID.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Nodnamnet [krävs] | Namnet på noden. |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta skickas vidare till motsvarande GetProgress API. |
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