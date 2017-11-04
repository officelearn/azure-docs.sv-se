---
title: Azure Service Fabric CLI - sfctl nod | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl nod-kommandon.
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
ms.openlocfilehash: 76037c7b4a2f7ada314a9360e3990245e6fbc06c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-node"></a>sfctl nod
Hantera de noder som formar ett kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
|    Inaktivera       | Inaktivera en Service Fabric-klusternod med angivna inaktiveringen syfte.|
|    Aktivera        | Aktivera en Service Fabric-klusternod som för tillfället inaktiverat.|
|    Hälsotillstånd        | Hämtar hälsotillståndet för en Service Fabric-nod.|
|    Info          | Hämtar listan över noder i Service Fabric-klustret.|
|    lista          | Hämtar listan över noder i Service Fabric-klustret.|
|    läsa in          | Hämtar belastningen information för en Service Fabric-nod.|
|    ta bort tillstånd  | Meddelar Service Fabric att det beständiga tillståndet på en nod har permanent bort eller tappas bort.|
|    rapporten hälsa | Skickar en hälsorapport på Service Fabric-nod.|
|    Starta om       | Startar om en klusternod för Service Fabric.|
|    övergång    | Startar eller stoppar en klusternod.|
|    övergången-status| Hämtar status för en åtgärd som är igång med StartNodeTransition.|


## <a name="sfctl-node-disable"></a>sfctl noden inaktivera
Inaktivera en Service Fabric-klusternod med angivna inaktiveringen syfte.

Inaktivera en Service Fabric-klusternod med angivna inaktiveringen syfte. När inaktiveringen pågår inaktiveringen avsikten kan ökade men inte minskas (till exempel en nod som är inaktiverat med paus syfte kan inaktiveras ytterligare med omstart, men inte tvärtom. Noder kan återaktiveras med aktivera node-åtgärden efter att de inaktiveras. Detta avbryter inaktiveringen om inaktiveringen inte har slutförts. En nod som stängs av och startas igen när inaktiveras måste fortfarande aktiveras innan tjänster kan placeras på noden.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs]| Namnet på noden.|
| --Inaktiveringen avsikt | Beskriver syftet eller anledningen till att inaktivera noden. Möjliga värden följande. -Pause - anger att noden ska pausas. Värdet är 1. -Omstart - anger att avsikten är att noden som ska startas om efter en kort tidsperiod. Värdet är 2. -RemoveData - anger avsikten är att noden för att ta bort data. Värdet är 3. .|
| --timeout -t       | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning            | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h          | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o        | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga            | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose          | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-node-enable"></a>sfctl noden aktivera
Aktivera en Service Fabric-klusternod som för tillfället inaktiverat.

Aktiverar en Service Fabric-klusternod som för tillfället inaktiverat. När aktiverad noden igen blir lönsam mål för att placera nya repliker och eventuella inaktiverad repliker kvar på noden har återaktiverats.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs]| Namnet på noden.|
| --timeout -t       | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning            | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h          | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o        | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga            | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose          | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-node-health"></a>sfctl noden hälsa
Hämtar hälsotillståndet för en Service Fabric-nod.

Hämtar hälsotillståndet för en Service Fabric-nod. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillstånd händelser rapporteras på den nod som baseras på hälsotillståndet. Om noden som du anger med namnet inte finns i health store, returneras ett fel.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs]| Namnet på noden.|
| --händelser-hälsa-tillstånd-filter| Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4). -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
| --timeout -t             | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning                  | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o              | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                  | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose                | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-node-info"></a>sfctl noden info
Hämtar listan över noder i Service Fabric-klustret.

Hämtar information om en viss nod i Service Fabric-klustret. Svaret innehåller namn, status, ID, hälsa, drifttid och annan information om noden.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs]| Namnet på noden.|
| --timeout -t       | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning            | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h          | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o        | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga            | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose          | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-node-list"></a>sfctl nodlistan
Hämtar listan över noder i Service Fabric-klustret.

Noder slutpunkten returnerar information om noderna i Service Fabric-kluster. Svaret innehåller namn, status, ID, hälsa, drifttid och annan information om noden.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --fortsättningstoken| Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tom värde ingår i svaret API när resultaten från systemet inte ryms i ett enda svar.      När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL.|
| --noden statusfiltret| Tillåter filtrering noder baserat på NodeStatus. Endast de noder som matchar det angivna filtret värdet returneras. Filtervärdet kan vara något av följande. -default - filtret värdet matchar alla noder excepts dem med status som okänd eller tagits bort. -alla - filtret värdet matchar alla noder. värdet matchar filtret - in - noder som är igång. värdet matchar filtret - ned - noder som är nere. -Aktivera - det här värdet matchar Filternoder som håller på att aktiveras med status som aktiverar. -inaktiverar – det här värdet matchar Filternoder som håller på att inaktiveras med status som inaktiverar. -inaktiverat - det här filtret värdet matchar noder som har inaktiverats. -Okänt - filtret värdet matchar noder vars status är okänd. En nod är okänt tillstånd om Service Fabric saknar auktoritär information om noden. Detta kan inträffa om systemet lär sig en nod vid körning. -ta bort - detta värde matchar Filternoder vars status är inställd på borttagen. Dessa är de noder som tas bort från klustret med RemoveNodeState-API. .      Standard: standard.|
| --timeout -t     | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning          | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h        | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o      | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga          | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose        | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-node-load"></a>Läs in sfctl-nod
Hämtar belastningen information för en Service Fabric-nod.

Hämtar belastningen information för en Service Fabric-nod.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs]| Namnet på noden.|
| --timeout -t       | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning            | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h          | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o        | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga            | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose          | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-node-restart"></a>sfctl noden omstart
Startar om en klusternod för Service Fabric.

Startar om en klusternod för Service Fabric som redan har startats.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs]| Namnet på noden.|
| – Skapa-fabric-dump  | Ange SANT om du vill skapa en dump av processen för fabric-nod. Detta är skiftlägeskänsliga.  Standard: False.|
| --nod-instans-id | Instans-ID för målnoden. Om instans-ID har angetts noden startas om du bara med den aktuella instansen av noden. Ett standardvärde på ”0” matchar alla instans-ID. Instans-ID kan hämtas med get-noden frågan.  Standard: 0.|
| --timeout -t       | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning            | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h          | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o        | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga            | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose          | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-node-transition"></a>sfctl noden övergång
Startar eller stoppar en klusternod.

Startar eller stoppar en klusternod.  En nod i klustret är en process, inte själva instansen OS.
Ange ”Start” för parametern NodeTransitionType om du vill starta en nod. Ange ”Avbryt” för parametern NodeTransitionType om du vill stoppa en nod. Detta API startar åtgärden - när API: N returnerar noden inte kan har slutförts övergång ännu. Anropa GetNodeTransitionProgress med samma åtgärds-ID att hämta status för åtgärden. .

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nod-instans-id [krävs]| I noden instans-ID på målnoden. Detta kan fastställas via GetNodeInfo API.|
| --nodnamn [krävs]| Namnet på noden.|
| ---övergång-nodtyp [krävs]| Anger vilken typ av övergång att utföra.                       NodeTransitionType.Start startar en stoppad nod.                       NodeTransitionType.Stop stoppar en nod som är igång. -Ogiltig - reserverade.  Överför inte till API-gränssnitt. -Start - övergång en stoppad nod till uppåt. -Stop - övergång en uppdaterad nod stoppas. .|
| --åtgärden-id [krävs]| Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress.|
| --stop-duration-i-sekunder [krävs]| Varaktighet i sekunder, att noden stoppades.  Det lägsta värdet är 600 är maximalt 14400. När denna tid har löpt ut startas noden automatiskt igen.|
| --timeout -t                      | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning                           | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                         | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                       | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.                       Standard: json.|
| --fråga                           | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose                         | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).