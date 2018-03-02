---
title: Azure Service Fabric CLI - sfctl replik | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl replik-kommandon.
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
ms.openlocfilehash: ba67a2a20d3f3e8e9fbccb2674cea500bfbde3fb
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-replica"></a>sfctl replica
Hantera replikerna som tillhör tjänsten partitioner.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
|    Distribueras  | Hämtar information om repliken har distribuerats på en Service Fabric-nod.|
|    distribuerat lista| Hämtar listan över repliker som har distribuerats på en Service Fabric-nod.|
|    hälsa    | Hämtar hälsotillståndet för Service Fabric tillståndskänslig service replik eller tillståndslösa tjänstinstansen.|
|    info      | Hämtar information om en replik av en Service Fabric-partition.|
|    lista      | Hämtar information om repliker för en partition för Service Fabric-tjänsten.|
|    ta bort    | Tar bort en replik för tjänsten som körs på en nod.|
|    report-health| Skickar en hälsorapport på Service Fabric-replik.|
|    Starta om   | Startar om tjänsten replik av en beständig tjänst som körs på en nod.|


## <a name="sfctl-replica-deployed"></a>sfctl repliken distribueras
Hämtar information om repliken har distribuerats på en Service Fabric-nod.

Hämtar information om repliken har distribuerats på en Service Fabric-nod. Informationen omfattar service typ, namn, aktuella tjänståtgärden, aktuella tjänståtgärden startar datum och tid, partitions-ID, repliken/instans-ID, rapporterade belastning och annan information.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs]| Namnet på noden.|
| --partitions-id [krävs]| Identitet för partitionen.|
| --replik-id [krävs]| Identifierare för repliken.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-replica-health"></a>sfctl repliken hälsa
Hämtar hälsotillståndet för Service Fabric tillståndskänslig service replik eller tillståndslösa tjänstinstansen.

Hämtar hälsotillståndet för en Service Fabric-replik. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillstånd händelser rapporteras på repliken som baseras på hälsotillståndet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs]| Identitet för partitionen.|
| --replik-id [krävs]| Identifierare för repliken.|
| --events-health-state-filter| Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4). -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
| --timeout -t             | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                  | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o              | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                  | JMESPath frågesträngen. Mer information finns i http://jmespath.org/.|
| -verbose                | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-replica-info"></a>sfctl repliken info
Hämtar information om en replik av en Service Fabric-partition.

Svaret innehåller ID: t, roll, status, hälsotillstånd, nodnamn, drifttid och annan information om repliken.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs]| Identitet för partitionen.|
| --replik-id [krävs]| Identifierare för repliken.|
| --continuation-token  | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tom värde ingår i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Mer information finns i http://jmespath.org/.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-replica-list"></a>sfctl replikeringslistan
Hämtar information om repliker för en partition för Service Fabric-tjänsten.

GetReplicas slutpunkten returnerar information om replikerna för den angivna partitionen.
Respons inkluderar ID, roll, status, hälsotillstånd, nodnamn, drifttid och annan information om repliken.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs]| Identitet för partitionen.|
| --continuation-token  | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tom värde ingår i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-replica-remove"></a>ta bort replik av sfctl
Tar bort en replik för tjänsten som körs på en nod.

Detta API simulerar ett Service Fabric-replik fel genom att ta bort en replik från ett Service Fabric-kluster. Borttagningen stängs repliken, övergår repliken till rollen ingen och sedan tar bort alla av statusinformation för replikeringen från klustret. Detta API testar replikeringssökvägen tillstånd borttagning och simulerar fel permanent rapportsökvägen via klientens API: er. Varning - det finns finns ingen säkerhet kontroll utförs när du använder detta API. Felaktig användning av denna API kan leda till dataförlust tillståndskänsliga tjänster. Dessutom påverkar flaggan forceRemove alla repliker finns i samma process.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs]| Namnet på noden.|
| --partitions-id [krävs]| Identitet för partitionen.|
| --replik-id [krävs]| Identifierare för repliken.|
| --force-remove        | Ta bort ett Service Fabric-program eller tjänst tvång utan att gå igenom alla korrekt avslutning. Den här parametern kan användas för att tvång ta bort ett program eller tjänst för vilka borttagning är avbryts på grund av problem i den kod som förhindrar korrekt Stäng av replikerna.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-replica-restart"></a>sfctl repliken omstart
Startar om tjänsten replik av en beständig tjänst som körs på en nod.

Startar om tjänsten replik av en beständig tjänst som körs på en nod. Varning - det finns finns ingen säkerhet kontroll utförs när du använder detta API. Felaktig användning av denna API kan leda till förlust av tillgänglighet för tillståndskänsliga tjänster.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs]| Namnet på noden.|
| --partitions-id [krävs]| Identitet för partitionen.|
| --replik-id [krävs]| Identifierare för repliken.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
