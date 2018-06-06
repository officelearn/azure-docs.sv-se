---
title: Azure Service Fabric CLI - sfctl klustret | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl cluster-kommandon.
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
ms.openlocfilehash: 60f3f74778f0fb32677c3b87b3140131ccd37bea
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763637"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Välj, hantera och driva Service Fabric-kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| koden-versioner | Hämtar en lista över fabric code-versioner som tillhandahålls i ett Service Fabric-kluster. |
| config-versioner | Hämtar en lista över fabric config-versioner som tillhandahålls i ett Service Fabric-kluster. |
| hälsotillstånd | Hämtar hälsotillståndet för ett Service Fabric-kluster. |
| Manifestet | Hämta Service Fabric-klustermanifestet. |
| Åtgärden Avbryt | Avbryter en egen användaren felåtgärd. |
| åtgärden-lista | Hämtar en lista över användare framkallas fel åtgärder filtreras efter angivna indata. |
| Etablera | Etablera kod eller konfigurationsfil paket i ett Service Fabric-kluster. |
| recover-system | Anger att den ska försöka återställa systemtjänster som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret. |
| rapporten hälsa | Skickar en hälsorapport på Service Fabric-klustret. |
| välj | Ansluter till en slutpunkt för Service Fabric-klustret. |
| Avetablera | Avetablera kod eller konfigurationsfil paket i ett Service Fabric-kluster. |
| Uppgradera | Börja uppgradera kod eller konfigurationsfil version av Service Fabric-klustret. |
| återuppta uppgradering | Se klusteruppgradering gå vidare till nästa uppgraderingsdomän. |
| uppgraderingen återställning | Återställa uppgradering av Service Fabric-klustret. |
| uppgradera status | Hämtar status för klustret uppgraderingen. |
| uppgraderingen-uppdatering | Uppdatera uppgradera parametrarna för en uppgradering av Service Fabric-klustret. |

## <a name="sfctl-cluster-code-versions"></a>sfctl klustret kod-versioner
Hämtar en lista över fabric code-versioner som tillhandahålls i ett Service Fabric-kluster.

Hämtar en lista med information om fabric code-versioner som tillhandahålls i klustret. Parametern CodeVersion kan användas för att du kan också filtrera utdata till endast att viss version.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --code-version | Produktversionen av Service Fabric. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-config-versions"></a>sfctl klustret config-versioner
Hämtar en lista över fabric config-versioner som tillhandahålls i ett Service Fabric-kluster.

Hämtar en lista med information om fabric config-versioner som tillhandahålls i klustret. Parametern ConfigVersion kan användas för att du kan också filtrera utdata till endast att viss version.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --config-version | Config-versionen av Service Fabric. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-health"></a>sfctl klustret hälsa
Hämtar hälsotillståndet för ett Service Fabric-kluster.

Hämtar hälsotillståndet för ett Service Fabric-kluster. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillstånd händelser som rapporterats i klustret som baseras på hälsotillståndet. På liknande sätt använder NodesHealthStateFilter ApplicationsHealthStateFilter att filtrera uppsättning noder och program som returneras baserat på deras aggregerade hälsotillstånd.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --applications-health-state-filter | Tillåter filtrering av programmets hälsotillstånd tillstånd objekten som returneras i resultatet av klustret hälsa fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärde från medlemmar eller binär åtgärder på medlemmar i HealthStateFilter-uppräkningen. Endast program som matchar filtret returneras. Alla program som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 hälsotillståndet för program med HealthState värdet OK (2) och varning (4) returneras.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --Utelämna hälsostatistik | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel. |
| --include-system-application-health-statistics | Anger om hälsostatistik bör innehålla infrastrukturresursen \: /system hälsostatistik för programmet. FALSKT som standard. Om IncludeSystemApplicationHealthStatistics är inställd på true och hälsotillståndet statistiken omfattar de enheter som hör till infrastrukturresursen \: /System program. Frågeresultatet innehåller annars hälsostatistik endast för program. Hälsostatistik måste inkluderas i frågeresultatet för den här parametern ska tillämpas. |
| --nodes-health-state-filter | Tillåter filtrering av noden hälsa tillstånd objekten som returneras i resultatet av klustret hälsa fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast de noder som matchar filtret returneras. Alla noder för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 hälsotillståndet för noder med HealthState värdet OK (2) och varning (4) returneras.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4.  <br> -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-manifest"></a>sfctl klustermanifestet
Hämta Service Fabric-klustermanifestet.

Hämta Service Fabric-klustermanifestet. Klustermanifestet innehåller egenskaper för klustret med olika nodtyper på klustret, säkerhetskonfigurationer, fel, och uppgraderingsdomänen topologier, osv. Dessa egenskaper anges som en del av filen ClusterConfig.JSON vid distribution av ett fristående kluster. Men de flesta av informationen i klustermanifestet genereras internt av service fabric under Klusterdistribution i andra scenarier för distribution (t.ex. när du använder Azure-portalen). Innehållet i klustermanifestet är endast i informationssyfte och användare förväntas inte koppla ett beroende på formatet på filens innehåll eller dess tolkning.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-operation-cancel"></a>Avbryt med sfctl klustret igen
Avbryter en egen användaren felåtgärd.

Följande API: er starta fel åtgärder som kan avbrytas med hjälp av CancelOperation: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Om Tvingad är FALSKT, sedan den angivna användare framkallas åtgärden att stoppas och rensa.  Om force är sant anger kommandot kommer att avbrytas vissa internt tillstånd lämnas kvar  Anger att framtvinga true ska användas med försiktighet. Anropar den här API: T med kraft inställd på true tillåts inte förrän den här API: T har redan anropats på samma test-kommando med kraft inställd på false första, eller om testkommandot har redan en OperationState OperationState.RollingBack. 

Förtydligande\: OperationState.RollingBack innebär att systemet blir/Rensa internt system tillstånd på grund av kommandot. Den kan inte återställa data om testkommandot skulle leda till dataförlust.  Till exempel om du anropa StartDataLoss sedan anropa denna API kommer systemet endast Rensa internt tillstånd från att köra kommandot. Den kan inte återställa den målpartitionen data om kommandot nått tillräckligt långt data gå förlorade. 

> [!NOTE]
> Om den här API anropas med kraft == true, internt tillstånd lämnas kvar.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta är skickades till API: et för motsvarande GetProgress. |
| --tvinga | Anger om du vill återställa smidigt och rensa interna systemtillstånd ändras genom att köra åtgärden framkallas av användaren. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-operation-list"></a>lista med sfctl klustret igen
Hämtar en lista över användare framkallas fel åtgärder filtreras efter angivna indata.

Hämtar listan över användare framkallas fel operations filtreras efter angivna indata.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --tillstånd-filter | Används för att filtrera på Operationstates för åtgärder som framkallas av användaren. <br> 65535 - Markera alla <br> 1 – Välj körs <br> 2 – Välj återställning av <br>8 – Välj slutförd <br>16 – Välj Faulted <br>32 – Välj avbrott <br>64 - Välj ForceCancelled.  <br>Som standard\: 65535. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |
| --Typ-filter | Används för att filtrera på OperationType för användaren framkallas åtgärder. <br> 65535 - Markera alla <br> 1 – Välj PartitionDataLoss. <br> 2 – Välj PartitionQuorumLoss. <br> 4 - Välj PartitionRestart. <br> 8 – Välj NodeTransition.  <br> Som standard\: 65535. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-provision"></a>sfctl klustret etablera
Etablera kod eller konfigurationsfil paket i ett Service Fabric-kluster.

Validera och etablera kod eller konfigurationsfil paket i ett Service Fabric-kluster.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --kluster-manifest--filsökväg | Klustret manifestfilen sökvägen. |
| --koden filsökväg | Filsökvägen för klustret kod paketet. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-recover-system"></a>sfctl klustret Återställ-system
Anger att den ska försöka återställa systemtjänster som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret.

Anger att den ska försöka återställa systemtjänster som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret. Den här åtgärden bör bara utföras om det är känt att replikerna är nere inte kan återställas. Felaktig användning av denna API kan orsaka förlust av data.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-report-health"></a>sfctl klustret rapport-hälsa
Skickar en hälsorapport på Service Fabric-klustret.

Rapporten innehålla information om källan för hälsorapport och egenskapen som har rapporterats. Rapporten skickas till ett Service Fabric gateway-noden, som sedan skickar till arkivet hälsa. Rapporten kan accepteras av gatewayen men avvisas av health store efter extra validering. Health store kan avvisa rapporten på grund av en ogiltig parameter som ett inaktuella sekvensnummer. Kontrollera att rapporten ska visas i HealthEvents i klustret för att se om rapporten har tillämpats i health store.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --hälsa egenskapen [krävs] | Egenskapen för information om hälsa. <br><br> En entitet kan ha hälsorapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport möjlighet att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan övervaka tillståndet för ledigt på en nod, så att den kan rapportera ”AvailableDisk”-egenskapen på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. I health store behandlas rapporterna som separata hälsa händelser för den angivna noden. Tillsammans med SourceId identifierar egenskapen fram hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden är\: 'Ogiltig', 'Ok', 'Varning', 'Fel', 'Okänt'. |
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

## <a name="sfctl-cluster-select"></a>sfctl kluster väljer
Ansluter till en slutpunkt för Service Fabric-klustret.

Om ansluter till säker kluster, ange en absolut sökväg till ett certifikat (.crt) och nyckelfilen (.key) eller en enstaka fil med både (.pem). Ange inte båda. Du kan också om ansluter till en säker klustret, ska du även ange en absolut sökväg till en CA paketfil eller katalog för certifikat för betrodd Certifikatutfärdare.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --endpoint [krävs] | Kluster slutpunkts-URL, inklusive porten och HTTP eller HTTPS-prefix. |
| --aad | Använda Azure Active Directory för autentisering. |
| --certifikatutfärdare | Absolut sökväg till CA-certifikat directory ska behandlas som giltiga eller Kanada paketfil. |
| --cert | Absolut sökväg till en klient-certifikatfil. |
| --nyckel | Absolut sökväg till klienten certifikatet nyckelfilen. |
| – Kontrollera Nej | Inaktivera verifiering för certifikat när du använder HTTPS, Observera\: detta är en osäker alternativ och ska inte användas i produktionsmiljöer. |
| --pem | Absolut sökväg till klientcertifikat som en PEM-filen. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-unprovision"></a>sfctl klustret avetablera
Avetablera kod eller konfigurationsfil paket i ett Service Fabric-kluster.

Avetablera kod eller konfigurationsfil paket i ett Service Fabric-kluster. Det går för att avetablera koden och konfiguration separat.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --code-version | Paketversion för klustret kod. |
| --config-version | Klustermanifestversionen. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-upgrade"></a>sfctl klusteruppgradering
Börja uppgradera kod eller konfigurationsfil version av Service Fabric-klustret.

Validera de angivna parametrarna för uppgraderingen och starta uppgraderingen kod eller konfigurationsfil version av Service Fabric-klustret om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --app hälsa mappning | JSON-kodade ordlista med par av namn och Maxprocent ohälsosamt innan du höjer fel. |
| --app typ-hälsa-mappning | JSON-kodade uppslagslista av par med programmets typnamn och Maxprocent ohälsosamt innan du höjer fel. |
| --code-version | Klustret code-versionen. |
| --config-version | Konfigurationsversionen för klustret. |
| --delta klienthälsa | Aktiverar delta hälsoutvärderingen i stället för absoluta hälsoutvärderingen efter varje domän har slutförts. |
| --delta felaktiga noder | Maximalt tillåten procentandel av noder hälsa försämras under uppgraderingar.  Som standard\: 10. <br><br> Delta mäts mellan tillståndet i noderna i början av uppgradering och tillståndet i noderna vid tiden för utvärderingen hälsa. Kontrollen utförs efter varje uppgradera uppgraderingsdomänen har slutförts och kontrollera globalt tillstånd i klustret är tillåten begränsas. |
| --åtgärd vid | Möjliga värden är\: 'Ogiltig ”,” återställa ”,” manuell ”. |
| --force-restart | Tvinga fram omstart. |
| --hälsa-check-återförsök | Health check försök timeout mätt i millisekunder. |
| --health check stabilt | Hälsokontroll stabil tid, mätt i millisekunder. |
| --hälsokontroll- | Health check vänta varaktighet mätt i millisekunder. |
| --replik-set-check-timeout | Uppgradera replikuppsättningen Kontrollera timeout mätt i sekunder. |
| --löpande uppgradering-läge | Möjliga värden är\: 'Ogiltig', 'UnmonitoredAuto', 'UnmonitoredManual', 'Övervakade'.  Som standard\: UnmonitoredAuto. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |
| --felaktiga program | Maximalt tillåten procentandel av felaktiga program innan ett fel rapporteras. <br><br> Om du vill tillåta 10% av program feltillstånd exempelvis är det här värdet 10. Procentandelen representerar maximalt tillåten procentandel av program som kan vara felaktiga innan klustret anses vara fel. Om procentandelen följs men det finns minst ett feltillstånd program, utvärderas hälsa som varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet programinstanser i klustret, exklusive programtyper som ingår i ApplicationTypeHealthPolicyMap-applikationer. Beräkningen Avrundar uppåt till tolererar att ett fel på mindre antal program. |
| --felaktiga noder | Maximalt tillåten procentandel av noder med fel innan den rapporterar ett fel. <br><br> Om du vill tillåta 10% av noder feltillstånd, exempelvis är det här värdet 10. Procentandelen representerar maximalt tillåten procentandel av noder som kan vara felaktiga innan klustret anses vara fel. Om procentandelen följs men det finns minst en nod i feltillstånd, utvärderas hälsa som varning. Procentandelen beräknas genom att dividera antalet felaktiga noder över det totala antalet noder i klustret. Beräkningen Avrundar uppåt till tolererar att ett fel på litet antal noder. I stora kluster kommer vissa noder alltid att ned eller ut för reparationer, så den här procentandelen ska konfigureras för att tolerera som. |
| --upgrade-domain-delta-unhealthy-nodes | Största tillåtna procentandelen uppgraderingsdomänen noder hälsa försämras under uppgraderingar.  Som standard\: 15. <br><br> Delta mäts mellan tillståndet i uppgraderingsdomänen noderna i början av uppgradering och tillståndet för uppgraderingsdomänen noder vid tiden för utvärderingen hälsa. Kontrollen utförs när varje uppgraderingsdomänen uppgradera slutförande för alla slutförts uppgraderingsdomäner att kontrollera tillståndet för uppgraderingsdomäner är tillåten begränsas. |
| --uppgraderingen domäntidsgräns | Uppgraderingsdomänen mätt i millisekunder. |
| --uppgraderingen-timeout | Tidsgränsen för uppgradering mätt i millisekunder. |
| --warning-as-error | Varningar behandlas med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-upgrade-resume"></a>Fortsätt med sfctl klustret uppgradering
Se klusteruppgradering gå vidare till nästa uppgraderingsdomän.

Se klustret kod eller konfigurationsfil uppgradera flyttningen i nästa uppgraderingsdomän om det är lämpligt.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --uppgraderingen domän [krävs] | Med nästa uppgraderingsdomän för den här uppgraderingen av klustret. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl klustret uppgraderingen-återställning
Återställa uppgradering av Service Fabric-klustret.

Återställa kod eller konfigurationsfil uppgradering av Service Fabric-klustret.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl klustret uppgraderingen-status
Hämtar status för klustret uppgraderingen.

Hämtar den aktuella statusen för pågående klustret uppgraderingen. Om ingen uppgradering pågår, Hämta senaste status för den tidigare uppgraderingen av klustret.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar. |
| --hjälp -h | Visa den här hjälpmeddelandet och avsluta. |
| --utdata -o | Format för utdata.  Tillåtna värden\: json jsonc, tabell, TVs.  Som standard\: json. |
| --fråga | JMESPath frågesträngen. Se http\://jmespath.org/ för mer information och exempel. |
| -verbose | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl klustret uppgraderingen-uppdatering
Uppdatera uppgradera parametrarna för en uppgradering av Service Fabric-klustret.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --app hälsa mappning | JSON-kodade ordlista med par av namn och Maxprocent ohälsosamt innan du höjer fel. |
| --app typ-hälsa-mappning | JSON-kodade uppslagslista av par med programmets typnamn och Maxprocent ohälsosamt innan du höjer fel. |
| --delta klienthälsa | Aktiverar delta hälsoutvärderingen i stället för absoluta hälsoutvärderingen efter varje domän har slutförts. |
| --delta felaktiga noder | Maximalt tillåten procentandel av noder hälsa försämras under uppgraderingar.  Som standard\: 10. <br><br> Delta mäts mellan tillståndet i noderna i början av uppgradering och tillståndet i noderna vid tiden för utvärderingen hälsa. Kontrollen utförs efter varje uppgradera uppgraderingsdomänen har slutförts och kontrollera globalt tillstånd i klustret är tillåten begränsas. |
| --åtgärd vid | Möjliga värden är\: 'Ogiltig ”,” återställa ”,” manuell ”. |
| --force-restart | Tvinga fram omstart. |
| --hälsa-check-återförsök | Health check försök timeout mätt i millisekunder. |
| --health check stabilt | Hälsokontroll stabil tid, mätt i millisekunder. |
| --hälsokontroll- | Health check vänta varaktighet mätt i millisekunder. |
| --replik-set-check-timeout | Uppgradera replikuppsättningen Kontrollera timeout mätt i sekunder. |
| --löpande uppgradering-läge | Möjliga värden är\: 'Ogiltig', 'UnmonitoredAuto', 'UnmonitoredManual', 'Övervakade'.  Som standard\: UnmonitoredAuto. |
| --timeout -t | Servern tidsgräns i sekunder.  Som standard\: 60. |
| --felaktiga program | Maximalt tillåten procentandel av felaktiga program innan ett fel rapporteras. <br><br> Om du vill tillåta 10% av program feltillstånd exempelvis är det här värdet 10. Procentandelen representerar maximalt tillåten procentandel av program som kan vara felaktiga innan klustret anses vara fel. Om procentandelen följs men det finns minst ett feltillstånd program, utvärderas hälsa som varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet programinstanser i klustret, exklusive programtyper som ingår i ApplicationTypeHealthPolicyMap-applikationer. Beräkningen Avrundar uppåt till tolererar att ett fel på mindre antal program. |
| --felaktiga noder | Maximalt tillåten procentandel av noder med fel innan den rapporterar ett fel. <br><br> Om du vill tillåta 10% av noder feltillstånd, exempelvis är det här värdet 10. Procentandelen representerar maximalt tillåten procentandel av noder som kan vara felaktiga innan klustret anses vara fel. Om procentandelen följs men det finns minst en nod i feltillstånd, utvärderas hälsa som varning. Procentandelen beräknas genom att dividera antalet felaktiga noder över det totala antalet noder i klustret. Beräkningen Avrundar uppåt till tolererar att ett fel på litet antal noder. I stora kluster kommer vissa noder alltid att ned eller ut för reparationer, så den här procentandelen ska konfigureras för att tolerera som. |
| --upgrade-domain-delta-unhealthy-nodes | Största tillåtna procentandelen uppgraderingsdomänen noder hälsa försämras under uppgraderingar.  Som standard\: 15. <br><br> Delta mäts mellan tillståndet i uppgraderingsdomänen noderna i början av uppgradering och tillståndet för uppgraderingsdomänen noder vid tiden för utvärderingen hälsa. Kontrollen utförs när varje uppgraderingsdomänen uppgradera slutförande för alla slutförts uppgraderingsdomäner att kontrollera tillståndet för uppgraderingsdomäner är tillåten begränsas. |
| --uppgraderingen domäntidsgräns | Uppgraderingsdomänen mätt i millisekunder. |
| --typ av uppgradering | Möjliga värden är\: 'Ogiltig', 'Rullande', 'Rolling_ForceRestart'.  Som standard\: rullande. |
| --uppgraderingen-timeout | Tidsgränsen för uppgradering mätt i millisekunder. |
| --warning-as-error | Varningar behandlas med samma allvarlighetsgrad som fel. |

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