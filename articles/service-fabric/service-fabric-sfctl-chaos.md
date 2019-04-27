---
title: Azure Service Fabric CLI - sfctl chaos | Microsoft Docs
description: Beskriver sfctl chaos-kommandon för Service Fabric CLI.
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
ms.openlocfilehash: b584ec301f0f4841c8df8fbbafb410abf645c373
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837359"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Starta, stoppa och rapportera om chaos-testtjänsten.

## <a name="subgroups"></a>Undergrupper
|Undergrupp|Beskrivning|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | Hämta och Schemalägg chaos. |
## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| evenemang | Hämtar händelserna Chaos utifrån fortsättningstoken eller tidsintervallet nästa segment. |
| Hämta | Hämta status för Chaos. |
| start | Startar Chaos i klustret. |
| Stanna | Stoppar Chaos om den körs i klustret och placerar Chaos-schema i ett stoppat tillstånd. |

## <a name="sfctl-chaos-events"></a>sfctl chaos händelser
Hämtar händelserna Chaos utifrån fortsättningstoken eller tidsintervallet nästa segment.

Du kan ange ContinuationToken för att hämta nästa segment Chaos-händelser. För att få början av ett nytt segment Chaos händelser kan ange du tidsintervallet via StartTimeUtc och EndTimeUtc. Du kan inte ange både ContinuationToken och tidsintervallet i samma anropet. När det finns fler än 100 Chaos händelser, Chaos händelser som returneras i flera segment där ett segment innehåller fler än 100 Chaos-händelser och att hämta nästa segment du göra ett anrop till den här API: et med fortsättningstoken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --continuation-token | Fortsättningstoken parameter-token som används för att hämta nästa uppsättning resultat. Ett fortsättningstoken med en icke-tomma värden inkluderas i svaret på API: et när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anropet API: et Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, innehåller ett värde inte i fortsättningstoken. Värdet för den här parametern får inte vara URL-kodas. |
| --end-time-utc | Windows-filen gång representerar sluttid på det tidsintervall som en Chaos-rapporten ska genereras. Läser [DateTime.ToFileTimeUtc metoden](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) mer information. |
| --max-results | Det maximala antalet resultat som ska returneras som en del av de växlade frågorna. Den här parametern definierar den övre gränsen för antalet resultat som returneras. Resultatet som returneras kan mindre än de angivna maximalt antal resultat om de inte passar i meddelandet enligt storleksbegränsningar max meddelande definieras i konfigurationen. Om den här parametern är noll eller inte har angetts, inkluderar växlade frågan så många resultat som möjligt som passar in i svarsmeddelandet. |
| --start-time-utc | Windows-filen tid som representerar starttiden på det tidsintervall som en Chaos-rapporten ska genereras. Läser [DateTime.ToFileTimeUtc metoden](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) mer information. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-chaos-get"></a>sfctl chaos get
Hämta status för Chaos.

Hämta status för Chaos som indikerar huruvida Chaos körs, Chaos-parametrar som används för att köra Chaos och status för Chaos-schema.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Startar Chaos i klustret.

Om Chaos inte körs i klustret, den börjar Chaos med det skickade i Chaos parametrar. Om Chaos redan körs när det här anropet görs anropet att misslyckas med felkoden FABRIC_E_CHAOS_ALREADY_RUNNING.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-type-health-policy-map | JSON-kodad lista med maximala procentandelen felaktiga program för specifika programtyper. Varje post anger som en nyckel med namnet på programmet och som ett värde som ett heltal som representerar procentandelen MaxPercentUnhealthyApplications används för att utvärdera program för den angivna typen. <br><br> Definierar en karta med maximala procentandelen felaktiga program för specifika programtyper. Varje post anger som nyckel namnet på programmet och som ett heltal som representerar procentandelen MaxPercentUnhealthyApplications används för att utvärdera program för den angivna programtypen-värde. Typ hälsotillstånd princip programkartan kan användas under klustret hälsotillstånd utvärdering för att beskriva särskilda programtyper. Programtyper som ingår i kartan utvärderas mot den procent som anges i kartan och inte med den globala MaxPercentUnhealthyApplications som definierats i hälsoprincip kluster. Program av programtyper som anges i kartan räknas inte mot den globala poolen av program. Till exempel om vissa program av en typ är viktiga, Klusteradministratören kan lägga till en post på kartan för den programtypen och tilldela den ett värde på 0% (det vill säga inte tål eventuella fel). Alla andra program kan utvärderas med MaxPercentUnhealthyApplications inställd på 20% ska kunna hanteras några fel av tusentals instanser av programmet. Typ hälsotillstånd princip programkartan används bara om klustermanifestet gör det möjligt för program typ hälsotillstånd utvärderingen med konfigurationsposten för HealthManager/EnableApplicationTypeHealthEvaluation. |
| --chaos-target-filter | JSON-kodad ordlistan med två strängnycklar typen. Två nycklar är både NodeTypeInclusionList och ApplicationInclusionList. Värden för båda dessa nycklar är en lista över strängar. chaos_target_filter definierar alla filter för riktade Chaos fel, till exempel felaktig endast vissa nodtyper eller felaktig endast vissa program. <br><br> Om inte chaos_target_filter används faults Chaos alla entiteter i klustret. Om du använder chaos_target_filter faults Chaos bara de enheter som uppfyller chaos_target_filter-specifikationen. Både NodeTypeInclusionList och ApplicationInclusionList kan en union semantik. Det går inte att ange en skärningspunkt för både NodeTypeInclusionList och ApplicationInclusionList. Exempel: Det går inte att ange ”felanalyser det här programmet endast när det är på den nodtypen”. När en entitet som ingår i både NodeTypeInclusionList och ApplicationInclusionList kan entiteten inte uteslutas med chaostargetfilter får. Även om applicationX inte visas i ApplicationInclusionList, i vissa Chaos iteration kan applicationX vara fel eftersom det rör sig på en nod i nodeTypeY som ingår i både NodeTypeInclusionList. Om både NodeTypeInclusionList och ApplicationInclusionList är tom, genereras ett ArgumentException. Alla typer av fel (starta om nod, starta om kodpaketet, ta bort repliken, starta om repliken, flytta primära och sekundära) är aktiverade för noder av typerna noden. Om en nodtyp (säga NodeTypeX) visas inte i NodeTypeInclusionList, och sedan noden på fel (till exempel NodeRestart) aldrig ska aktiveras för noderna i NodeTypeX, men paketet och repliken fel i koden kan fortfarande aktiveras för NodeTypeX om ett program i den ApplicationInclusionList råkar finnas på en nod i NodeTypeX. Högst 100 noden typnamnen kan tas med i listan om du vill öka antalet, en uppgradering av konfiguration krävs för MaxNumberOfNodeTypesInChaosEntityFilter konfiguration. Alla repliker som hör till tjänster av dessa program är lämpar sig för replik-fel (starta om replikeringen, ta bort repliken, flytta primär och flytta sekundär) av Chaos. Chaos kan behöva starta om ett kodpaket endast om kodpaketet är värd för repliker av dessa program endast. Om ett program inte visas i den här listan, kan det fortfarande vara fel i vissa Chaos iteration om programmet slutar på en nod i en nodtyp som ingår i både NodeTypeInclusionList. Men om applicationX är knuten till nodeTypeY via placeringsbegränsningar och applicationX saknas från ApplicationInclusionList och nodeTypeY saknas från NodeTypeInclusionList, sedan applicationX ska aldrig vara felaktiga. Högst 1000 programnamn kan tas med i listan om du vill öka antalet, en uppgradering av konfiguration krävs för MaxNumberOfApplicationsInChaosEntityFilter konfiguration. |
| --context | JSON-kodad karta över (sträng, sträng) Ange nyckel / värde-par. Kartan kan användas för att registrera information om Chaos-körningen. Det får inte finnas fler än 100 par och varje sträng (nyckel eller ett värde) får vara högst 4095 tecken långt. Den här kartan anges med starter Chaos kör för att lagra kontext om den specifika körningen. |
| --disable-move-replica-faults | Inaktiverar den flytta primärt och flytta sekundära fel. |
| --max-cluster-stabilization | Längsta väntetiden för alla entiteter för att blir stabila och felfri.  Standard\: 60. <br><br> Chaos körs i iterationer och i början av varje iteration kontrollerar hälsotillståndet för klustret entiteter. Vid verifiering av om en entitet i klustret inte är stabil och felfri inom MaxClusterStabilizationTimeoutInSeconds, genererar Chaos en misslyckad verifiering-händelse. |
| --max-concurrent-faults | Det maximala antalet samtidiga fel initierats per iteration. Chaos körs i iterationer och två på varandra följande iterationer skiljs åt av en valideringsfasen. Ju högre samtidighet, mer aggressivt inmatning av fel--förmå mer komplexa serie tillstånd att få fram buggar. Rekommendationen är att börja med ett värde på 2 eller 3 och försiktig när du flyttar.  Standard\: 1. |
| --max-percent-unhealthy-apps | När du utvärderar klusterhälsa under Chaos, högsta tillåtna procentandel program som är felaktiga innan den rapporterar ett fel. <br><br> Högsta tillåtna procentandel program som är felaktiga innan den rapporterar ett fel. Om du vill tillåta 10% av program vara felaktig, blir det här värdet 10. Procentandelen representerar högsta tolererat procentandelen av program som kan vara felaktig innan klustret anses av misstag. Om procentandelen respekteras men det finns minst en felaktigt program, utvärderas hälsotillståndet som varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet instanser av programmet i klustret, exklusive program typer av program som ingår i ApplicationTypeHealthPolicyMap. Beräkningen Avrundar uppåt till tolerera fel på ett fel på små mängder program. Standard är noll. |
| --max-percent-unhealthy-nodes | När du utvärderar klusterhälsa under Chaos, högsta tillåtna procentandel av defekta noder innan den rapporterar ett fel. <br><br> Högsta tillåtna procentandel av defekta noder innan den rapporterar ett fel. Om du vill tillåta 10% av noder vara felaktig, blir det här värdet 10. Procentandelen representerar maximalt tolererat procentandel noder som kan vara felaktig innan klustret anses av misstag. Om procentandelen respekteras men det finns minst en felaktig nod, utvärderas hälsotillståndet som varning. I procent beräknas genom att dividera antalet defekta noder över det totala antalet noder i klustret. Beräkningen Avrundar uppåt till tolerera fel på ett fel på små antal noder. Standard är noll. I stora kluster kommer vissa noder alltid att ned eller ut för reparationer, så att den här procentandelen ska konfigureras för att kunna hanteras som. |
| --time-to-run | Total tid (i sekunder) som Chaos ska köras innan du stoppar automatiskt. Det högsta tillåtna värdet är 4 294 967 295 (System.UInt32.MaxValue).  Standard\: 4294967295. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --Vänta tid mellan fel | Väntetid (i sekunder) mellan på varandra följande fel inom en enda iteration.  Standard\: 20. <br><br> Ju större värde, desto lägre överlappning mellan fel och den enklare serie tillstånd övergår som går igenom för klustret. Rekommendationen är att börja med ett värde mellan 1 och 5 och Övning varning vid förflyttning av. |
| --wait-time-between-iterations | Time-uppdelning (i sekunder) mellan två på varandra följande iterationer Chaos. Ju större värde, desto lägre frekvensen för fel-inmatning.  Standard\: 30. |
| --warning-as-error | Anger om varningar behandlas med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
Stoppar Chaos om den körs i klustret och placerar Chaos-schema i ett stoppat tillstånd.

Stoppar Chaos från att köra nya fel. Pågående fel fortsätter att köra tills de har slutförts. Det aktuella schemat Chaos placeras i ett stoppat tillstånd. När ett schema har stoppats, kommer den stanna kvar i ett stoppat tillstånd och inte användas till att Chaos schema nya körningar av Chaos. Ett nytt schema Chaos måste anges för att återuppta schemaläggning.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
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