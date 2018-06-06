---
title: Azure Service Fabric CLI - sfctl nod | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl nod-kommandon.
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
ms.openlocfilehash: fb8a310a131938e95f3d21b3962dbbd1944a57ed
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763433"
---
# <a name="sfctl-node"></a>sfctl node
Hantera de noder som formar ett kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| inaktivera | Inaktivera en Service Fabric-klusternod med angivna inaktiveringen syfte. |
| aktivera | Aktivera ett Service Fabric-klusternod som för tillfället inaktiverat. |
| hälsotillstånd | Hämtar hälsotillståndet för en Service Fabric-nod. |
| info | Hämtar information om en viss nod i Service Fabric-klustret. |
| lista | Hämtar listan över noder i Service Fabric-klustret. |
| läsa in | Hämtar belastningen information för en Service Fabric-nod. |
| ta bort tillstånd | Meddelar Service Fabric att det beständiga tillståndet på en nod har permanent bort eller tappas bort. |
| rapporten hälsa | Skickar en hälsorapport på Service Fabric-nod. |
| Starta om | Startar om en klusternod för Service Fabric. |
| övergång | Startar eller stoppar en klusternod. |
| övergången-status | Hämtar status för en åtgärd som är igång med StartNodeTransition. |

## <a name="sfctl-node-disable"></a>sfctl noden inaktivera
Inaktivera en Service Fabric-klusternod med angivna inaktiveringen syfte.

Inaktivera en Service Fabric-klusternod med angivna inaktiveringen syfte. När inaktiveringen pågår inaktiveringen avsikten kan ökade men inte minskas (till exempel en nod som är inaktiverat med paus syfte kan inaktiveras ytterligare med omstart, men inte tvärtom. Noder kan återaktiveras med aktivera node-åtgärden efter att de inaktiveras. Om inte inaktiveringen är klar, avbryts inaktiveringen. En nod som stängs av och startas igen när inaktiveras fortfarande måste aktiveras innan tjänster kommer att placeras på noden.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| --Inaktiveringen avsikt | Beskriver syftet eller anledningen till att inaktivera noden. Möjliga värden följande. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-node-enable"></a>sfctl noden aktivera
Aktivera ett Service Fabric-klusternod som för tillfället inaktiverat.

Aktiverar ett Service Fabric-klusternod som för tillfället inaktiverat. När aktiverad noden igen blir lönsam mål för att placera nya repliker och eventuella inaktiverad repliker kvar på noden ska återaktiveras.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-node-health"></a>sfctl noden hälsa
Hämtar hälsotillståndet för en Service Fabric-nod.

Hämtar hälsotillståndet för en Service Fabric-nod. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillstånd händelser rapporteras på den nod som baseras på hälsotillståndet. Om noden som du anger med namnet inte finns i health store, returneras ett fel.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
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

## <a name="sfctl-node-info"></a>sfctl noden info
Hämtar information om en viss nod i Service Fabric-klustret.

Hämtar information om en viss nod i Service Fabric-kluster. Svaret innehåller namn, status, ID, hälsa, drifttid och annan information om noden.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-node-list"></a>sfctl nodlistan
Hämtar listan över noder i Service Fabric-klustret.

Hämtar listan över noder i Service Fabric-klustret. Svaret innehåller namn, status, ID, hälsa, drifttid och annan information om noden.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --fortsättningstoken | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett tomt värde inkluderas i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL. |
| --node-status-filter | Tillåter filtrering noder baserat på NodeStatus. Endast de noder som matchar det angivna filtret värdet returneras. Filtervärdet kan vara något av följande.  Standard\: standard. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-node-load"></a>Läs in sfctl-nod
Hämtar belastningen information för en Service Fabric-nod.

Hämtar belastningen information för en Service Fabric-nod för de mätvärden som har belastning eller kapacitet har definierats.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-node-remove-state"></a>sfctl noden remove-tillstånd
Meddelar Service Fabric att det beständiga tillståndet på en nod har permanent bort eller tappas bort.

Meddelar Service Fabric att det beständiga tillståndet på en nod har permanent bort eller tappas bort.  Detta innebär att det inte går att återställa det beständiga tillståndet noder. Detta inträffar vanligtvis om en hårddisk har rensats ren, eller om en hårddisk kraschar. Noden har inte är tillgänglig för den här åtgärden ska lyckas. Den här åtgärden kan Service Fabric vet att replikerna på noden inte längre finns och att Service Fabric ska avbrytas väntar på att dessa replikerna är igång. Kör inte denna cmdlet om noden tillstånd inte har tagits bort och noden kan komma tillbaka på dess tillstånd intakta.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-node-report-health"></a>sfctl noden rapport-hälsa
Skickar en hälsorapport på Service Fabric-nod.

Rapporterar hälsotillståndet för den angivna Service Fabric-noden. Rapporten innehålla information om källan för hälsorapport och egenskapen som har rapporterats. Rapporten skickas till ett Service Fabric gateway-noden, som sedan skickar till arkivet hälsa. Rapporten kan accepteras av gatewayen men avvisas av health store efter extra validering. Health store kan avvisa rapporten på grund av en ogiltig parameter som ett inaktuella sekvensnummer. Kontrollera att rapporten ska visas i avsnittet HealthEvents för att se om rapporten har tillämpats i health store.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --hälsa egenskapen [krävs] | Egenskapen för information om hälsa. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport möjlighet att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan övervaka tillståndet för ledigt på en nod, så att den kan rapportera ”AvailableDisk”-egenskapen på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. I health store behandlas rapporterna som separata hälsa händelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen fram hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden är\: 'Ogiltig', 'Ok', 'Varning', 'Fel', 'Okänt'. |
| --nodnamn [krävs] | Nodnamnet att rapportera om. |
| --käll-id [krävs] | Källnamn som identifierar komponenten klient-watchdog-systemet som genererats fram hälsoinformation. |
| – Beskrivning | Beskrivning av informationen om hälsa. <br><br> Den representerar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4096 tecken. Den angivna strängen är längre, trunkeras den automatiskt. När trunkerad, de sista tecknen i beskrivningen innehåller en markör ”[Truncated]” och totala strängens storlek är 4096 tecken. Förekomst av markören som anger att användare som trunkering inträffade. Observera att när trunkerad, beskrivningen har mindre än 4096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till Service Fabric-gateway som vidarebefordrar till health store. Om Immediate anges till true, rapporten skickas direkt från http-Gateway i health store, oavsett fabric-klientinställningarna som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas rapporten skickas fortfarande, till exempel om HTTP-Gateway är stängt eller meddelandet inte nå gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningar hälsa från HTTP-Gateway. Det är därför batchar enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom det tillåter hälsa klienten att optimera hälsotillståndsrapportering meddelanden till health store, samt för bearbetning. Som standard skickas rapporter inte omedelbart. |
| – ta bort när-har upphört | Värde som anger om rapporten tas bort från health store när den upphör. <br><br> Om värdet är true, rapporten har tagits bort från health store när den upphör. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). Det här sättet är personen har problem (t.ex. deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
| ---sekvensnummer | Sekvensnumret för den här hälsorapport som en numerisk sträng. <br><br> Sekvensnummer rapporten används av health store för att identifiera inaktuella rapporter. Om inget anges genereras ett sekvensnummer automatiskt av klientens hälsa när en rapport har lagts till. |
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

## <a name="sfctl-node-restart"></a>sfctl noden omstart
Startar om en klusternod för Service Fabric.

Startar om en klusternod för Service Fabric som redan har startats.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| – Skapa-fabric-dump | Ange SANT om du vill skapa en dump av processen för fabric-nod. Detta är skiftlägeskänsliga.  Som standard\: False. |
| --nod-instans-id | Instans-ID för målnoden. Om instans-ID har angetts noden startas om du bara med den aktuella instansen av noden. Ett standardvärde på ”0” matchar alla instans-ID. Instans-ID kan hämtas med get-noden frågan.  Som standard\: 0. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-node-transition"></a>sfctl noden övergång
Startar eller stoppar en klusternod.

Startar eller stoppar en klusternod.  En nod i klustret är en process, inte själva instansen OS.  Ange ”Start” för parametern NodeTransitionType om du vill starta en nod. Ange ”Avbryt” för parametern NodeTransitionType om du vill stoppa en nod. Detta API startar åtgärden - när API: N returnerar noden inte kan har slutförts övergång ännu. Anropa GetNodeTransitionProgress med samma åtgärds-ID att hämta status för åtgärden.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nod-instans-id [krävs] | I noden instans-ID på målnoden. Detta kan fastställas via GetNodeInfo API. |
| --nodnamn [krävs] | Namnet på noden. |
| ---övergång-nodtyp [krävs] | Anger vilken typ av övergång att utföra.  NodeTransitionType.Start startar en stoppad nod. NodeTransitionType.Stop stoppas en nod som är igång. |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress. |
| --stop-duration-i-sekunder [krävs] | Varaktighet i sekunder, att noden stoppades.  Det lägsta värdet är 600 är maximalt 14400.  När denna tid har löpt ut kommer noden automatiskt tillbaka. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-node-transition-status"></a>sfctl nod övergång-status
Hämtar status för en åtgärd som är igång med StartNodeTransition.

Hämtar status för en åtgärd som är igång med StartNodeTransition med hjälp av det tillhandahållna åtgärds-ID.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --nodnamn [krävs] | Namnet på noden. |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress. |
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