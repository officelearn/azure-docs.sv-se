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
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: dbea84511c37cf52c3d98f0247e5ce3c0b2a05c3
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-chaos"></a>sfctl chaos
Starta, stoppa och rapportera om tjänsten chaos test.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
|    rapport| Hämtar Chaos rapporten utifrån skickades i fortsättningstoken eller skickades i-tidsintervallet nästa segment.|
|    start | Om Chaos inte redan körs i klustret, startar Chaos med angivet i Chaos parametrar.|
|    Stanna  | Stoppar Chaos i klustret om det redan körs, annars den ingen effekt.|


## <a name="sfctl-chaos-report"></a>sfctl chaos rapport
Hämtar Chaos rapporten utifrån skickades i fortsättningstoken eller skickades i-tidsintervallet nästa segment.

Du kan antingen ange ContinuationToken för att få rapporten Chaos nästa segment eller du kan ange tidsintervall via StartTimeUtc och EndTimeUtc, men du kan inte ange både ContinuationToken och tidsintervallet i samma anropet. När det finns fler än 100 Chaos händelser, returneras Chaos rapporten segment där ett segment innehåller fler än 100 Chaos händelser. 

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --continuation-token| Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tom värde ingår i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL.|
| --utc-tid-slutpunkt   | Antalet skalstreck som representerar sluttiden för det tidsintervall som en Chaos rapporten ska genereras. Kontakta [DateTime.Ticks egenskapen](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) för ytterligare information om skalstreck.|
| --utc-tid-start | Antalet skalstreck som representerar starttiden för tidsintervallet som en Chaos rapporten ska genereras. Kontakta [DateTime.Ticks egenskapen](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) för ytterligare information om skalstreck.|
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
Om Chaos inte redan körs i klustret, startar Chaos med angivet i Chaos parametrar.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --app-type-health-policy-map  | JSON-kodade lista med högsta procentandelen felaktiga program för specifika programtyper. Varje post anger som en nyckel med namnet på programmet och som ett heltal som representerar MaxPercentUnhealthyApplications procentandelen som används för att utvärdera program av typen angivet program.|
| --disable-move-replica-faults | Inaktiverar den flytta primärt och flytta sekundär fel.|
| --max-kluster-stabilisering| Den maximala mängden väntetiden för alla enheter blir stabila och felfri.  Standard: 60.|
| --max-concurrent-faults    | Det maximala antalet samtidiga fel framkallas per iteration.           Standard: 1.|
| --max-percent-unhealthy-apps  | När du utvärderar kluster under Chaos tillåtna procentandelen felaktiga program innan ett fel rapporteras.|
| --max-percent-unhealthy-nodes | Största tillåtna procentandelen felaktiga noder innan den rapporterar ett fel vid utvärdering av kluster under Chaos.|
| --time-to-run              | Total tid (i sekunder) som Chaos ska köras innan du stoppar automatiskt. Det högsta tillåtna värdet är 4 294 967 295 (System.UInt32.MaxValue).  Standard: 4294967295.|
| --timeout -t               | Servern tidsgräns i sekunder.  Standard: 60.|
| --Vänta tid mellan fel | Väntetid (i sekunder) mellan på varandra följande fel i en enda iteration.  Standard: 20.|
| --Vänta tid mellan iterationer| Tid-uppdelning (i sekunder) mellan två på varandra följande iterationer kaotisk.  Standard: 30.|
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