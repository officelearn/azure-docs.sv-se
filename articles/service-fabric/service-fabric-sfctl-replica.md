---
title: Azure Service Fabric CLI - sfctl replik | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl replik-kommandon.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cd09fe906f77bb06f0ac7afaa6c6cce326dbfa5c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763586"
---
# <a name="sfctl-replica"></a>sfctl replica
Hantera replikerna som tillhör tjänsten partitioner.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| Distribueras | Hämtar information om repliken har distribuerats på en Service Fabric-nod. |
| distribuerat lista | Hämtar listan över repliker som har distribuerats på en Service Fabric-nod. |
| hälsotillstånd | Hämtar hälsotillståndet för Service Fabric tillståndskänslig service replik eller tillståndslösa tjänstinstansen. |
| info | Hämtar information om en replik av en Service Fabric-partition. |
| lista | Hämtar information om repliker för en partition för Service Fabric-tjänsten. |
| ta bort | Tar bort en replik för tjänsten som körs på en nod. |
| rapporten hälsa | Skickar en hälsorapport på Service Fabric-replik. |
| Starta om | Startar om tjänsten replik av en beständig tjänst som körs på en nod. |

## <a name="sfctl-replica-deployed"></a>sfctl repliken distribueras
Hämtar information om repliken har distribuerats på en Service Fabric-nod.

Hämtar information om repliken har distribuerats på en Service Fabric-nod. Informationen omfattar service typ, namn, aktuella tjänståtgärden, aktuella tjänståtgärden startar datum och tid, partitions-ID, repliken/instans-ID, rapporterade belastningen och annan information.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --replik-id [krävs] | Identifierare för repliken. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-replica-deployed-list"></a>distribuerat sfctl-replik-lista
Hämtar listan över repliker som har distribuerats på en Service Fabric-nod.

Hämtar en lista som innehåller information om repliker som har distribuerats på en Service Fabric-nod. Informationen omfattar partitions-ID, replik-ID, status för replikering, namnet på tjänsten, namnet på tjänsttypen och annan information. Använd PartitionId eller ServiceManifestName frågeparametrar för att returnera information om distribuerade repliker som matchar de angivna värdena för dessa parametrar.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs] | Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ' fabric\:-URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”\~” tecken. Om programnamnet är till exempel ”fabric\:/myapp/app1”, programidentiteten skulle vara ”myapp\~app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --nodnamn [krävs] | Namnet på noden. |
| --partitions-id | Identitet för partitionen. |
| --Manifestet tjänstnamn | Namnet på ett service manifest som registrerats som en del av en typ av program i ett Service Fabric-kluster. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-replica-health"></a>sfctl repliken hälsa
Hämtar hälsotillståndet för Service Fabric tillståndskänslig service replik eller tillståndslösa tjänstinstansen.

Hämtar hälsotillståndet för en Service Fabric-replik. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillstånd händelser rapporteras på repliken som baseras på hälsotillståndet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identitet för partitionen. |
| --replik-id [krävs] | Identifierare för repliken. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-replica-info"></a>sfctl repliken info
Hämtar information om en replik av en Service Fabric-partition.

Svaret innehåller ID: T, roll, status, hälsotillstånd, nodnamn, drifttid och annan information om repliken.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identitet för partitionen. |
| --replik-id [krävs] | Identifierare för repliken. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-replica-list"></a>sfctl replikeringslistan
Hämtar information om repliker för en partition för Service Fabric-tjänsten.

GetReplicas slutpunkten returnerar information om replikerna för den angivna partitionen. Svaret innehåller ID: T, roll, status, hälsotillstånd, nodnamn, drifttid och annan information om repliken.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --partitions-id [krävs] | Identitet för partitionen. |
| --fortsättningstoken | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett tomt värde inkluderas i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-replica-remove"></a>ta bort replik av sfctl
Tar bort en replik för tjänsten som körs på en nod.

Detta API simulerar ett Service Fabric-replik fel genom att ta bort en replik från ett Service Fabric-kluster. Borttagningen stängs repliken, övergår repliken till rollen ingen och sedan tar bort alla av statusinformation för replikeringen från klustret. Detta API testar replikeringssökvägen tillstånd borttagning och simulerar fel permanent rapportsökvägen via klientens API: er. Varning - det finns finns ingen säkerhet kontroll utförs när du använder detta API. Felaktig användning av denna API kan leda till dataförlust tillståndskänsliga tjänster. Dessutom påverkar flaggan forceRemove alla repliker finns i samma process.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --replik-id [krävs] | Identifierare för repliken. |
| --force-ta bort | Ta bort ett Service Fabric-program eller tjänst tvång utan att gå igenom alla korrekt avslutning. Den här parametern kan användas för att tvång ta bort ett program eller tjänst för vilka borttagning är avbryts på grund av problem i den kod som förhindrar korrekt Stäng av replikerna. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-replica-report-health"></a>sfctl repliken rapport-hälsa
Skickar en hälsorapport på Service Fabric-replik.

Rapporterar hälsotillståndet för den angivna Service Fabric-repliken. Rapporten innehålla information om källan för hälsorapport och egenskapen som har rapporterats. Rapporten skickas till Service Fabric-gateway replik som vidarebefordrar till health store. Rapporten kan accepteras av gatewayen men avvisas av health store efter extra validering. Health store kan avvisa rapporten på grund av en ogiltig parameter som ett inaktuella sekvensnummer. Kontrollera att rapporten ska visas i avsnittet händelser för att se om rapporten har tillämpats i health store.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --hälsa egenskapen [krävs] | Egenskapen för information om hälsa. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport möjlighet att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan övervaka tillståndet för ledigt på en nod, så att den kan rapportera ”AvailableDisk”-egenskapen på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. I health store behandlas rapporterna som separata hälsa händelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen fram hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden är\: 'Ogiltig', 'Ok', 'Varning', 'Fel', 'Okänt'. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --replik-id [krävs] | Identitet för partitionen. |
| --käll-id [krävs] | Källnamn som identifierar komponenten klient-watchdog-systemet som genererats fram hälsoinformation. |
| – Beskrivning | Beskrivning av informationen om hälsa. <br><br> Den representerar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4096 tecken. Den angivna strängen är längre, trunkeras den automatiskt. När trunkerad, de sista tecknen i beskrivningen innehåller en markör ”[Truncated]” och totala strängens storlek är 4096 tecken. Förekomst av markören som anger att användare som trunkering inträffade. Observera att när trunkerad, beskrivningen har mindre än 4096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till Service Fabric-gateway som vidarebefordrar till health store. Om Immediate anges till true, rapporten skickas direkt från http-Gateway i health store, oavsett fabric-klientinställningarna som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas rapporten skickas fortfarande, till exempel om HTTP-Gateway är stängt eller meddelandet inte nå gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningar hälsa från HTTP-Gateway. Det är därför batchar enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom det tillåter hälsa klienten att optimera hälsotillståndsrapportering meddelanden till health store, samt för bearbetning. Som standard skickas rapporter inte omedelbart. |
| – ta bort när-har upphört | Värde som anger om rapporten tas bort från health store när den upphör. <br><br> Om värdet är true, rapporten har tagits bort från health store när den upphör. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). Det här sättet är personen har problem (t.ex. deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
| ---sekvensnummer | Sekvensnumret för den här hälsorapport som en numerisk sträng. <br><br> Sekvensnummer rapporten används av health store för att identifiera inaktuella rapporter. Om inget anges genereras ett sekvensnummer automatiskt av klientens hälsa när en rapport har lagts till. |
| --service-typen | Typ av tjänst replik (tillståndslösa och tillståndskänsliga) som hälsan rapporteras. Följande är möjliga värden\: 'Tillståndslös', 'Stateful'.  Som standard\: Stateful. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |
| --TTL-värde | Den tid som den här hälsorapport är giltigt. Det här fältet formatet ISO8601 för att ange varaktigheten. <br><br> När klienterna rapporterar med jämna mellanrum, skickar de rapporter med högre frekvens än livslängd. De kan ange time to live till oändliga om klienter rapport i övergång. När time to live-upphör att gälla hälsohändelse som innehåller information om hälsa är antingen bort från health store om RemoveWhenExpired är true och utvärderas vid fel, om RemoveWhenExpired false. Om inte tidpunkten på live oändligt värde som standard. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-replica-restart"></a>sfctl repliken omstart
Startar om tjänsten replik av en beständig tjänst som körs på en nod.

Startar om tjänsten replik av en beständig tjänst som körs på en nod. Varning - det finns finns ingen säkerhet kontroll utförs när du använder detta API. Felaktig användning av denna API kan leda till förlust av tillgänglighet för tillståndskänsliga tjänster.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| --partitions-id [krävs] | Identitet för partitionen. |
| --replik-id [krävs] | Identifierare för repliken. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
