---
title: Azure Service Fabric CLI - sfctl choas | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl chaos-kommandon.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 34e4d47b1de509c2053996d9d1078733d7055447
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-chaos"></a>sfctl chaos
Starta, stoppa och rapportera om tjänsten chaos test.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
|    rapport| Hämtar Chaos rapporten utifrån skickades i fortsättningstoken eller skickades i-tidsintervallet nästa segment.|
|    start | Startar Chaos i klustret.|
|    Stanna  | Stoppar Chaos i klustret om det redan körs, annars den ingen effekt.|


## <a name="sfctl-chaos-report"></a>sfctl chaos rapport
Hämtar Chaos rapporten utifrån skickades i fortsättningstoken eller skickades i-tidsintervallet nästa segment.

Du kan antingen ange ContinuationToken för att få rapporten Chaos nästa segment eller du kan ange tidsintervall via StartTimeUtc och EndTimeUtc, men du kan inte ange både ContinuationToken och tidsintervallet i samma anropet. När det finns fler än 100 Chaos händelser, returneras Chaos rapporten segment där ett segment innehåller fler än 100 Chaos händelser. 

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --continuation-token| Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tom värde ingår i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL.|
| --utc-tid-slutpunkt   | Windows-filen tid som representerar sluttiden för det tidsintervall som en Chaos rapporten ska genereras. Kontakta [DateTime.ToFileTimeUtc metoden](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) mer information.|
| --utc-tid-start | Windows-filen tid som representerar starttiden för tidsintervallet som en Chaos rapporten ska genereras. Kontakta [DateTime.ToFileTimeUtc metoden](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) mer information.|
| --timeout -t     | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug          | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h        | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o      | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga          | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose        | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Startar Chaos i klustret.

Om Chaos inte körs i klustret, den börjar Chaos med den överförda i Chaos parametrar. Anropet misslyckas med felkoden FABRIC_E_CHAOS_ALREADY_RUNNING Chaos redan körs när det här anropet görs.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --app-type-health-policy-map  | JSON-kodade lista med högsta procentandelen felaktiga program för specifika programtyper. Varje post anger som en nyckel med namnet på programmet och som ett heltal som representerar MaxPercentUnhealthyApplications procentandelen som används för att utvärdera program av typen angivet program. Definierar en mappning med högsta procentandelen felaktiga program för specifika programtyper. Varje post anger som nyckel programmets typnamn och som ett heltal som representerar MaxPercentUnhealthyApplications procentandelen som används för att utvärdera program för den angivna programtypen-värde. Programmet hälsa princip typkarta kan användas under hälsoutvärderingen för klustret för att beskriva specialprogram typer. Programtyper som ingår i kartan utvärderas mot procentandelen som anges i mappningen, och inte med den globala MaxPercentUnhealthyApplications som definierats i hälsoprincipen klustret. Program för program som angetts i kartan räknas inte mot den globala poolen av program. Till exempel om vissa program av en typ som är viktiga Klusteradministratören kan lägga till en post på kartan för programmet och tilldela den värdet 0% (d.v.s. inte tolerera fel). Alla andra program kan utvärderas med MaxPercentUnhealthyApplications angetts till 20% till tolerera några fel utanför tusentals programinstanser. Programmet hälsa princip typkarta används bara om klustermanifestet gör det möjligt för program typen hälsa utvärderingen med konfigurationsposten för HealthManager/EnableApplicationTypeHealthEvaluation.|
|--chaos-target-filter         | JSON-kodade ordlista med två typen strängnycklar. Två nycklar är NodeTypeInclusionList och ApplicationInclusionList. Värden för båda nycklarna är stränglista. chaos_target_filter definierar alla filter för riktade Chaos fel, till exempel felaktig endast vissa nodtyper eller felaktig endast vissa program. Om inte chaos_target_filter används hos Chaos alla entiteter i klustret. Om chaos_target_filter används hos Chaos de enheter som uppfyller chaos_target_filter-specifikationen. NodeTypeInclusionList och ApplicationInclusionList kan en union semantik. Det går inte att ange en skärningspunkt för NodeTypeInclusionList och ApplicationInclusionList. T.ex, går det inte att ange ”fault det här programmet endast när det är för att nodtypen”. När en entitet ingår i NodeTypeInclusionList eller ApplicationInclusionList kan entiteten inte uteslutas med ChaosTargetFilter. Även om applicationX inte visas i ApplicationInclusionList, i vissa Chaos iteration kan applicationX vara fel eftersom det sker på en nod i nodeTypeY som ingår i NodeTypeInclusionList. Om både NodeTypeInclusionList och ApplicationInclusionList är tom genereras ett ArgumentException. Alla typer av fel (starta om nod, starta om kodpaketet, ta bort replik, starta om repliken, flytta primära och sekundära) har aktiverats för noder av nodtyperna. Om en nodtyp (säga NodeTypeX) visas inte i NodeTypeInclusionList, och sedan noden nivån fel (till exempel NodeRestart) aldrig ska aktiveras för noderna i NodeTypeX, men koden paketet och repliken fel kan fortfarande aktiveras för NodeTypeX om ett program i den ApplicationInclusionList råkar finnas på en nod i NodeTypeX. Högst 100 typen nodnamn kan ingå i den här listan för att öka antalet, en config-uppgradering krävs för MaxNumberOfNodeTypesInChaosEntityFilter konfiguration. Alla repliker som hör till av dessa program lämpar sig för att repliken fel (starta om repliken, ta bort replik, flytta primära och flytta sekundära) genom Chaos. Chaos kan starta om en kodpaketet endast om kodpaketet värd replikerna för dessa program bara. Om ett program inte visas i listan, kan det fortfarande vara fel i vissa Chaos iteration om programmet som hamnar på en nod av typen som ingår i NodeTypeInclusionList. Men om applicationX är knutna till nodeTypeY via placeringen och applicationX saknas från ApplicationInclusionList och nodeTypeY saknas från NodeTypeInclusionList, sedan applicationX aldrig blir felaktig. Högst 1000 programnamn kan ingå i den här listan för att öka antalet, en config-uppgradering krävs för MaxNumberOfApplicationsInChaosEntityFilter konfiguration.|
|--context                     | JSON-kodade karta över (sträng, sträng) anger nyckel-värdepar. Kartan kan användas för att registrera information om Chaos kör. Det får inte finnas fler än 100 par och varje sträng (nyckel eller ett värde) får innehålla högst 4095 tecken. Den här kartan anges av starter kaotisk kör för att lagra kontexten om specifika kör.|
| --disable-move-replica-faults | Inaktiverar den flytta primärt och flytta sekundär fel.|
| --max-kluster-stabilisering| Den maximala mängden väntetiden för alla enheter blir stabila och felfri.  Standard: 60. Chaos körs i iterationer och i början av varje iteration kontrolleras hälsotillståndet för klustret entiteter. Vid verifiering av om en kluster-enhet inte är stabilt och felfri inom MaxClusterStabilizationTimeoutInSeconds, genererar Chaos en misslyckad validering-händelse.|
| --max-concurrent-faults    | Det maximala antalet samtidiga fel framkallas per iteration.           Standard: 1. Chaos körs i iterationer och två på varandra följande iterationer skiljs åt av en valideringsfasen. Ju högre samtidighet, mer aggressivt injektion av fel--att mer komplicerade att upptäcka fel. Rekommendationen är att börja med ett värde på 2 eller 3 och vara försiktig när du flyttar.|
| --max-percent-unhealthy-apps  | När du utvärderar kluster under Chaos tillåtna procentandelen felaktiga program innan ett fel rapporteras. Maximalt tillåten procentandel av felaktiga program innan ett fel rapporteras. Om du vill tillåta 10% av program feltillstånd exempelvis är det här värdet 10. Procentandelen representerar maximalt tillåten procentandel av program som kan vara felaktiga innan klustret anses vara fel. Om procentandelen följs men det finns minst ett feltillstånd program, utvärderas hälsa som varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet programinstanser i klustret, exklusive programtyper som ingår i ApplicationTypeHealthPolicyMap-applikationer. Beräkningen Avrundar uppåt till tolererar att ett fel på mindre antal program. Standardmässiga procentandelen är noll.|
| --max-percent-unhealthy-nodes | Största tillåtna procentandelen felaktiga noder innan den rapporterar ett fel vid utvärdering av kluster under Chaos. Maximalt tillåten procentandel av noder med fel innan den rapporterar ett fel. Om du vill tillåta 10% av noder feltillstånd, exempelvis är det här värdet 10. Procentandelen representerar maximalt tillåten procentandel av noder som kan vara felaktiga innan klustret anses vara fel. Om procentandelen följs men det finns minst en nod i feltillstånd, utvärderas hälsa som varning. Procentandelen beräknas genom att dividera antalet felaktiga noder över det totala antalet noder i klustret. Beräkningen Avrundar uppåt till tolererar att ett fel på litet antal noder. Standardmässiga procentandelen är noll. I stora kluster kommer vissa noder alltid att ned eller ut för reparationer, så den här procentandelen ska konfigureras för att tolerera som.|
| --time-to-run              | Total tid (i sekunder) som Chaos ska köras innan du stoppar automatiskt. Det högsta tillåtna värdet är 4 294 967 295 (System.UInt32.MaxValue).  Standard: 4294967295.|
| --timeout -t               | Servern tidsgräns i sekunder.  Standard: 60.|
| --Vänta tid mellan fel | Väntetid (i sekunder) mellan på varandra följande fel i en enda iteration.  Standard: 20. Ju större värde, desto längre ned överlappande mellan fel och den enklare sekvensen av tillstånd övergår som går igenom för klustret. Rekommendationen är att börja med ett värde mellan 1 och 5 och Övning försiktig när du flyttar.|
| --Vänta tid mellan iterationer| Tid-uppdelning (i sekunder) mellan två på varandra följande iterationer kaotisk. Ju större värde, desto lägre frekvens för fel-injection. Standard: 30.|
| --warning-as-error         | När du utvärderar kluster under Chaos behandla varningar med samma allvarlighetsgrad som fel.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                    | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                  | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.           Standard: json.|
| --fråga                    | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose                  | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-chaos-stop"></a>sfctl chaos stoppa
Stoppar Chaos i klustret om det redan körs, annars den ingen effekt.

Stoppar Chaos från schemaläggning av ytterligare fel; men relä fel påverkas inte.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --timeout -t| Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug  | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h| Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga  | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose| Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).