---
title: Azure Service Fabric CLI- sfctl kaos
description: Lär dig mer om sfctl, kommandoradsgränssnittet i Azure Service Fabric. Innehåller en lista med kommandon för att hantera kaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6668446363361fbc6d24afc3d11a36a0b786667d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906161"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Starta, stoppa och rapportera om kaostesttjänsten.

## <a name="subgroups"></a>Undergrupper
|Undergrupp|Beskrivning|
| --- | --- |
| [Schema](service-fabric-sfctl-chaos-schedule.md) | Ställ in kaosschemat. |
## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| händelser | Hämtar nästa segment av Chaos-händelserna baserat på fortsättningstoken eller tidsintervallet. |
| get | Få status som Chaos. |
| start | Startar Kaos i klustret. |
| stoppa | Stoppar Kaos om det körs i klustret och placerar Chaos Schedule i ett stoppat tillstånd. |

## <a name="sfctl-chaos-events"></a>sfctl kaos händelser
Hämtar nästa segment av Chaos-händelserna baserat på fortsättningstoken eller tidsintervallet.

Om du vill hämta nästa segment av Kaoshändelserna kan du ange ContinuationToken. Om du vill starta ett nytt segment av Chaos-händelser kan du ange tidsintervallet via StartTimeUtc och EndTimeUtc. Du kan inte ange både ContinuationToken och tidsintervallet i samma anrop. När det finns fler än 100 Chaos-händelser returneras Kaos-händelserna i flera segment där ett segment inte innehåller mer än 100 Chaos-händelser och för att få nästa segment som du ringer ett anrop till det här API:et med fortsättningstoken.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --fortsättning-token | Parametern för fortsättningstoken används för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tomt värde inkluderas i svaret på API:et när resultaten från systemet inte får plats i ett enda svar. När det här värdet skickas till nästa API-anrop returnerar API:et nästa uppsättning resultat. Om det inte finns några ytterligare resultat innehåller fortsättningstoken inget värde. Värdet för den här parametern bör inte URL-kodas. |
| --end-time-utc | Windows-filtiden som representerar sluttiden för det tidsintervall som en Chaos-rapport ska genereras för. Mer information finns i [DateTime.ToFileTimeUtc-metoden.](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) |
| --max-resultat | Det maximala antalet resultat som ska returneras som en del av de växlingsdelade frågorna. Den här parametern definierar den övre gränsen för antalet returnerade resultat. De returnerade resultaten kan vara mindre än de angivna maximala resultaten om de inte får plats i meddelandet enligt de begränsningar för högsta meddelandestorlek som definierats i konfigurationen. Om den här parametern är noll eller inte angiven innehåller den växlingsbara frågan så många resultat som möjligt som passar i returmeddelandet. |
| --start-time-utc | Windows-filtiden som representerar starttiden för det tidsintervall som en Chaos-rapport ska genereras för. Mer information finns i [DateTime.ToFileTimeUtc-metoden.](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) |
| --timeout -t | Tidsgränsen för servern för att utföra åtgärden på några sekunder. Den här timeouten anger den tid som klienten är villig att vänta på att den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-chaos-get"></a>sfctl kaos få
Få status som Chaos.

Få status för Chaos som anger om Chaos körs, kaosparametrarna som används för att köra Chaos och statusen för Chaos Schedule.

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

## <a name="sfctl-chaos-start"></a>sfctl kaos start
Startar Kaos i klustret.

Om Chaos inte redan körs i klustret startar det Chaos med de passerade i Chaos-parametrarna. Om Chaos redan körs när det här samtalet görs misslyckas anropet med felkoden FABRIC_E_CHAOS_ALREADY_RUNNING. Mer information finns i artikeln [Inducerade kontrollerade Chaos in Service Fabric-kluster.](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos)

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-typ-hälsa-policy-karta | JSON kodade lista med max procent felaktiga program för specifika programtyper. Varje post anger som en nyckel programtypens namn och som ett värde ett heltal som representerar den Procent av MaxPercentUnhealthyApplications som används för att utvärdera programmen för den angivna programtypen. <br><br> Definierar en karta med felen för maxprocent för specifika programtyper. Varje post anger som nyckel programtypens namn och som värde ett heltal som representerar procentprocenten MaxPercentUnhealthyApplications som används för att utvärdera programmen av den angivna programtypen. Hälsoprincipmappningen för programtyp kan användas vid utvärdering av klusterhälsa för att beskriva särskilda programtyper. De programtyper som ingår i kartan utvärderas mot den procentandel som anges i kartan och inte med de globala MaxPercentUnhealthyApplications som definierats i klusterhälsoprincipen. De program av programtyper som anges i kartan räknas inte mot den globala programpoolen. Om vissa program av en typ till exempel är kritiska kan klusteradministratören lägga till en post på kartan för den programtypen och tilldela den ett värde på 0 % (det vill än några fel). Alla andra program kan utvärderas med MaxPercentUnhealthyApplications inställd på 20% för att tolerera vissa fel av de tusentals programinstanser. Hälsoprincipmappningen för programtyp används endast om klustermanifestet aktiverar hälsoutvärdering av programtyp med hjälp av konfigurationsposten för HealthManager/EnableApplicationTypeHealthEvaluation. |
| --kaos-mål-filter | JSON kodade ordlista med två strängtypsnycklar. De två nycklarna är NodeTypeInclusionList och ApplicationInclusionList. Värden för båda dessa nycklar är en lista över sträng. chaos_target_filter definierar alla filter för riktade Chaos-fel, till exempel fel endast vissa nodtyper eller fel endast vissa program. <br><br> Om chaos_target_filter inte används, fel chaos alla kluster entiteter. Om chaos_target_filter används, chaos fel endast de entiteter som uppfyller chaos_target_filter specifikationen. NodeTypeInclusionList och ApplicationInclusionList tillåter endast en unionssanfallsmantik. Det går inte att ange en skärningspunkt mellan NodeTypeInclusionList och ApplicationInclusionList. Det går till exempel inte att ange "fel det här programmet endast när det är på den nodtypen." När en entitet har inkluderats i nodeTypeInclusionList eller ApplicationInclusionList kan den entiteten inte uteslutas med ChaosTargetFilter. Även om applicationX inte visas i ApplicationInclusionList kan det i vissa Chaos iteration applicationX vara fel eftersom det råkar vara på en nod av nodeTypeY som ingår i NodeTypeInclusionList. Om både NodeTypeInclusionList och ApplicationInclusionList är tomma, genereras ett ArgumentException. Alla typer av fel (omstartsnod, omstartskodpaket, ta bort replik, starta om replik, flytta primärt och flytta sekundärt) är aktiverade för noderna för dessa nodtyper. Om en nodtyp (säg nodeTypeX) inte visas i NodeTypeInclusionList aktiveras nodnivåfel (som NodeRestart) aldrig för noderna nodTypeX, men kodpaket och replikfel kan fortfarande aktiveras för NodeTypeX om ett program i ApplicationInclusionList råkar finnas på en nod node Av NodeTypeX. Högst 100 nodtypnamn kan inkluderas i den här listan, för att öka det här antalet krävs en konfigurationsuppgradering för MaxNumberOfNodeTypesInChaosEntityFilter-konfiguration. Alla repliker som tillhör tjänster av dessa program är mottagliga för replikfel (starta om replik, ta bort replik, flytta primärt och flytta sekundärt) av Chaos. Kaos kan starta om ett kodpaket endast om kodpaketet endast är värd för repliker av dessa program. Om ett program inte visas i den här listan kan det fortfarande vara fel i vissa Chaos iteration om programmet hamnar på en nod av en nodtyp som ingår i NodeTypeInclusionList. Men om applicationX är knuten till nodeTypeY genom placeringsbegränsningar och applicationX saknas från ApplicationInclusionList och nodeTypeY saknas från NodeTypeInclusionList, kommer applicationX aldrig att fel. Högst 1000 programnamn kan inkluderas i den här listan, för att öka detta antal krävs en konfigurationsuppgradering för MaxNumberOfApplicationsInChaosEntityFilter-konfiguration. |
| --sammanhang | JSON kodade karta över (sträng, sträng) typ nyckel-värde par. Kartan kan användas för att registrera information om Chaos-körningen. Det kan inte finnas fler än 100 sådana par och varje sträng (nyckel eller värde) kan vara högst 4095 tecken lång. Den här kartan ställs in av startmotorn för Chaos-körningen för att eventuellt lagra kontexten om den specifika körningen. |
| --disable-move-replica-faults --disable-move-replica-faults --disable-move-replica-faults -- | Inaktiverar flytta primära och flytta sekundära fel. |
| --max-cluster-stabilisering | Den maximala tiden att vänta på att alla klusterentiteter ska bli stabila och felfria.  Standard\: 60. <br><br> Kaos körs i iterationer och i början av varje iteration validerar det hälsotillståndet för klusterentiteter. Under valideringen om en klusterentitet inte är stabil och felfri inom MaxClusterStabilizationTimeoutInSeconds genererar Chaos en verifieringslös händelse. |
| --max-samtidiga-fel | Det maximala antalet samtidiga fel som induceras per iteration. Kaos körs i iterationer och två på varandra följande iterationer avgränsas av en valideringsfas. Ju högre samtidighet, desto mer aggressiv injektion av fel - inducera mer komplexa serier av stater för att avslöja buggar. Rekommendationen är att börja med ett värde av 2 eller 3 och att iaktta försiktighet när du flyttar upp.  Standard\: 1. |
| --max-procent-ohälsosam-apps | Vid utvärdering av klusterhälsa under Chaos, den högsta tillåtna procentandelen felaktiga program innan du rapporterar ett fel. <br><br> Den högsta tillåtna procentandelen felaktiga program innan du rapporterar ett fel. Om du till exempel vill att 10 % av programmen ska vara felaktiga, skulle det här värdet vara 10. Procentsatsen representerar den maximala tolererade procentandelen av program som kan vara felaktiga innan klustret betraktas som ett fel. Om procentsatsen respekteras men det finns minst ett felaktigt program utvärderas hälsotillståndet som Varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet programinstanser i klustret, exklusive program av programtyper som ingår i ApplicationTypeHealthPolicyMap. Uträkningen avrundar upp till tolerera ett fel på litet nummer av applikationer. Standardprocenten är noll. |
| --max-procent-ohälsosam-noder | Vid utvärdering av klusterhälsa under Chaos, den högsta tillåtna procentandelen felaktiga noder innan du rapporterar ett fel. <br><br> Den högsta tillåtna procentandelen felaktiga noder innan du rapporterar ett fel. Om du till exempel vill att 10 % av noderna ska vara felaktiga, skulle det här värdet vara 10. Procentsatsen representerar den maximala tolererade procentandelen noder som kan vara felaktiga innan klustret betraktas som ett fel. Om procentsatsen respekteras men det finns minst en felaktig nod utvärderas hälsotillståndet som Varning. Procentsatsen beräknas genom att dividera antalet felaktiga noder över det totala antalet noder i klustret. Beräkningen avrundar upp för att tolerera ett fel på ett litet antal noder. Standardprocenten är noll. I stora kluster kommer vissa noder alltid att vara nere eller ute för reparationer, så den här procentsatsen bör konfigureras för att tolerera det. |
| --time-to-run | Total tid (i sekunder) som Chaos körs för innan du automatiskt stoppar. Det högsta tillåtna värdet är 4 294 967 295 (System.UInt32.MaxValue).  Standard\: 4294967295. |
| --timeout -t | Standard\: 60. |
| --vänta-tid-mellan-fel | Väntetid (i sekunder) mellan på varandra följande fel inom en enda iteration.  Standard\: 20. <br><br> Ju större värde, desto lägre överlappar varandra mellan fel och enklare sekvens av tillståndsövergångar som klustret går igenom. Rekommendationen är att börja med ett värde mellan 1 och 5 och iaktta försiktighet när du flyttar upp. |
| --vänta-tid-mellan-iterationer | Tidsseparation (i sekunder) mellan två på varandra följande iterationer av Chaos. Ju större värde, desto lägre felinsprutningshastighet.  Standard\: 30. |
| --varning-som-fel | Anger om varningar behandlas med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka loggningsverbaliteten för att visa alla felsökningsloggar. |
| --hjälp -h | Visa det här hjälpmeddelandet och avsluta. |
| --utgång -o | Utdataformat.  Tillåtna\: värden json, jsonc, tabell, tsv.  Standard\: json. |
| --fråga | JMESPath-frågesträng. Mer\:information och exempel finns på http //jmespath.org/. |
| --utförlig | Öka loggningsverbaliteten. Använd --debug för fullständiga felsökningsloggar. |

## <a name="sfctl-chaos-stop"></a>sfctl kaos stopp
Stoppar Kaos om det körs i klustret och placerar Chaos Schedule i ett stoppat tillstånd.

Stoppar Chaos från att utföra nya fel. Under flygning fel kommer att fortsätta att köra tills de är klara. Det aktuella kaosschemat stoppas. När ett schema har stoppats, kommer det att stanna i stoppat tillstånd och inte användas för att Chaos Schedule nya körningar av Chaos. Ett nytt Chaos-schema måste anges för att återuppta schemaläggningen.

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


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric CLI.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempelskripten](/azure/service-fabric/scripts/sfctl-upgrade-application).