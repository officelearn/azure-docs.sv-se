---
title: "Azure Service Fabric CLI - sfctl utgöra | Microsoft Docs"
description: "Beskriver Service Fabric CLI sfctl utgöra kommandon."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 3010c298cf227c761288365e3663ffe3fb67d863
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-compose"></a>Skapa sfctl
Skapa, ta bort och hantera distributioner av Docker Compose.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
|    skapa| Distribuera ett Service Fabric-program från en Skriv-fil.|
|    lista  | Hämtar listan över utgöra distributioner som skapats i Service Fabric-klustret.|
|   Ta bort| Tar bort en befintlig Service Fabric skapa distribution från klustret.|
|   status| Hämtar information om ett Service Fabric skapa distributionen.|
|Uppgradera       | Börja uppgradera en Skriv-distribution i Service Fabric-klustret.|
|    uppgradera status| Hämtar information om den senaste uppgraderingen utförs på den här Service Fabric skapa distributionen.|


## <a name="sfctl-compose-create"></a>Skapa sfctl skapa
Skapar ett Service Fabric skapa distributionen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --filsökvägen [krävs]| Sökväg till målfilen Docker Compose.|
 |   --distributionsnamnet [krävs]| Namnet på distributionen.|
|    --krypterade pass             | I stället för att fråga efter behållaren registret lösenord, använda en redan krypterade lösenfras.|
|    --har pass                   | Frågar efter ett lösenord i registret för behållaren.|
|    --timeout -t                 | Servern tidsgräns i sekunder.  Standard: 60.|
 |   --användare                       | Användarnamn för att ansluta till registret för behållaren.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning                 | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h               | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o             | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                 | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose               | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-compose-list"></a>sfctl utgöra lista
Hämtar listan över utgöra distributioner som skapats i Service Fabric-klustret.

Hämtar status om Skriv-distributioner som har skapats eller håller på att skapas i Service Fabric-klustret. Svaret innehåller namn, status och annan information om Skriv distributionen. Om distributioner inte får plats på en sida, returneras en sida av resultat samt fortsättningstoken som kan användas för att hämta nästa sida.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --fortsättningstoken| Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tom värde ingår i svaret API när resultaten från systemet inte ryms i ett enda svar.      När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL.|
| --max-resultat    | Högsta antal resultat ska returneras som en del av växlingsbar frågor.      Den här parametern anger den övre gränsen för antalet resultat som returneras.      Om de inte passar i meddelandet enligt max meddelandet storlek restriktioner som definierats i konfigurationen kan resultaten vara mindre än de angivna maximala resultat. Om den här parametern är noll eller inte har angetts, inkludera växlingsbara frågorna så många resultat som möjligt som ryms i svarsmeddelandet.|
| --timeout -t     | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning          | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h        | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o      | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga          | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose        | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-compose-remove"></a>sfctl utgöra ta bort
Tar bort en befintlig Service Fabric skapa distribution från klustret.

Tar bort en befintlig Service Fabric skapa distributionen. 

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --distributionsnamnet [krävs]| Identiteten för distributionen. Detta är vanligtvis det fullständiga namnet på programmet utan den ”fabric:' URI-schema.|
| --timeout -t            | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning                 | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h               | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o             | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                 | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose               | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-compose-status"></a>sfctl utgöra status
Hämtar information om ett Service Fabric skapa distributionen.

Returnerar status för utgöra distribution som har skapats eller håller på att skapas i Service Fabric-kluster och vars namn matchar den som anges som parameter. Svaret innehåller namn, status och annan information om programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --distributionsnamnet [krävs]| Identiteten för distributionen. |
| --timeout -t            | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning                 | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h               | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o             | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                 | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose               | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-compose-upgrade"></a>sfctl utgöra uppgradering
Börja uppgradera en Skriv-distribution i Service Fabric-klustret.

Validerar angivna uppgradera parametrarna och börja uppgradera distributionen.

### <a name="arguments"></a>Argument
|Argumentet|Beskrivning|
| --- | --- |
|    --filsökvägen [krävs]| Sökväg till målfilen Docker Compose.|
|    --distributionsnamnet [krävs]| Namnet på distributionen.|
|    – standard-svc-typ-hälsa-karta| JSON-kodade ordlista som beskriver hälsoprincipen används för att utvärdera hälsan för tjänster.|
|    --krypterade pass             | I stället för att fråga efter behållaren registret lösenord, använda en redan krypterade lösenfras.|
 |   --åtgärd vid             | Möjliga värden är: 'Ogiltig ”,” återställa ”,” manuell ”.|
|    --Tvingad omstart              | Tvinga fram omstart.|
 |   --har pass                   | Frågar efter ett lösenord i registret för behållaren.|
|    --hälsa-check-återförsök         | Health check försök timeout mätt i millisekunder.|
|    --health check stabilt        | Hälsokontroll stabil tid, mätt i millisekunder.|
|    --hälsokontroll-          | Health check vänta varaktighet mätt i millisekunder.|
|    --replik-set-kontroll          | Uppgradera replikuppsättningen Kontrollera timeout mätt i sekunder.|
|    --svc-typ-hälsa-karta        | JSON-kodad lista över objekt som beskriver hälsoprinciper som används för att utvärdera hälsan för olika typer.|
|    --timeout -t                 | Servern tidsgräns i sekunder.  Standard: 60.|
|    --ohälsosamt-app              | Maximalt tillåten procentandel av felaktiga program innan ett fel rapporteras.        Om du vill tillåta 10% av program feltillstånd exempelvis är det här värdet 10. Procentandelen representerar maximalt tillåten procentandel av program som kan vara felaktiga innan klustret anses vara fel. Om procentandelen följs men det finns minst ett feltillstånd program, utvärderas hälsa som varning. Den här procent beräknas genom att dividera antalet felaktiga program över det totala antalet programinstanser i klustret.|
|    --uppgraderingen domäntidsgräns     | Tidsgränsen för uppgraderingsdomänen mätt i millisekunder.|
|    --typ av uppgradering               | Standard: rullande.|
|    --uppgraderingen-läge               | Möjliga värden är: ”Ogiltig', 'UnmonitoredAuto', 'UnmonitoredManual', 'Övervakade'.  Standard: UnmonitoredAuto.|
|    --uppgraderingen-timeout            | Uppgradera timeout mätt i millisekunder.|
|    --användare                       | Användarnamn för att ansluta till registret för behållaren.|
|    --varning som fel           | Varningar behandlas med samma allvarlighetsgrad som fel.|

### <a name="global-arguments"></a>Globala argument
 |Argumentet|Beskrivning|
| --- | --- |
|   – Felsökning                      | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
|    --hjälp -h                    | Visa den här hjälpmeddelandet och avsluta.|
 |   --utdata -o                  | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.
                                   Standard: json.|
 |   --fråga                      | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
 |   -verbose                    | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="next-steps"></a>Nästa steg
- [Ställ in](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).