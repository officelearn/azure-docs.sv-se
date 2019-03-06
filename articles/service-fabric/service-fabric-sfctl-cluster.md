---
title: Azure Service Fabric CLI - sfctl-kluster | Microsoft Docs
description: Beskriver sfctl cluster-kommandon för Service Fabric CLI.
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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: e3aae5f7936204a7fe4fbce4102fc2727088e025
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442039"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Välj, hantera och driva Service Fabric-kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| code-versions | Hämtar en lista över fabric code-versioner som har etablerats i ett Service Fabric-kluster. |
| config-versions | Hämtar en lista över fabric config-versioner som har etablerats i ett Service Fabric-kluster. |
| hälsa | Hämtar hälsotillståndet för Service Fabric-kluster. |
| Manifest | Hämta Service Fabric-klustermanifestet. |
| Åtgärden Avbryt | Avbryter en egen användaren felåtgärd. |
| åtgärden-list | Hämtar en lista över användare egen fel åtgärder filtreras efter angivna indata. |
| etablera | Etablera kodning eller paket i ett Service Fabric-kluster. |
| recover-system | Anger att den ska försöka att återställa systemtjänster som för närvarande har fastnat i förlorar kvorum till Service Fabric-klustret. |
| rapportera hälsa | Skickar en hälsorapport på Service Fabric-klustret. |
| välj | Ansluter till en slutpunkt för Service Fabric-kluster. |
| show-connection | Visa vilka Service Fabric-kluster som den här sfctl-instansen är ansluten till. |
| avetablera | Avetablera kodning eller paket i ett Service Fabric-kluster. |
| uppgradering | Börja uppgradera kodning eller version av Service Fabric-kluster. |
| uppgraderingen återuppta | Kontrollera klusteruppgraderingen gå vidare till nästa uppgraderingsdomän. |
| uppgraderingen-återställning | Återställa uppgradering av Service Fabric-kluster. |
| uppgraderingen-status | Hämtar förloppet för den aktuella uppgraderingen av klustret. |
| uppdatering av uppgradering | Uppdatera Uppgraderingsparametrar för en uppgradering av Service Fabric-klustret. |

## <a name="sfctl-cluster-code-versions"></a>sfctl kod-klusterversioner
Hämtar en lista över fabric code-versioner som har etablerats i ett Service Fabric-kluster.

Hämtar en lista med information om fabric code-versioner som har etablerats i klustret. Parametern CodeVersion kan användas för att du kan också filtrera utdata till endast den specifika versionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --code-version | Produktversionen för Service Fabric. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-config-versions"></a>sfctl config-klusterversioner
Hämtar en lista över fabric config-versioner som har etablerats i ett Service Fabric-kluster.

Hämtar en lista med information om fabric config-versioner som har etablerats i klustret. Parametern ConfigVersion kan användas för att du kan också filtrera utdata till endast den specifika versionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --config-version | Config-versionen av Service Fabric. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-health"></a>sfctl klusterhälsa
Hämtar hälsotillståndet för Service Fabric-kluster.

Använd EventsHealthStateFilter för att filtrera insamling av health-händelser som rapporterats i klustret som baseras på hälsotillståndet. På samma sätt kan använda NodesHealthStateFilter och ApplicationsHealthStateFilter att filtrera insamling av noder och program som returneras baserat på deras aggregerade hälsotillstånd.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --applications-health-state-filter | Tillåter filtrering av application health tillstånd objekten som returneras i resultatet av klustret hälsotillstånd fråga baserat på deras hälsotillstånd. De möjliga värdena för den här parametern innehåller heltalsvärde har fått från medlemmar eller bitvis åtgärder på medlemmar i HealthStateFilter uppräkning. Endast program som matchar filtret returneras. Alla program som används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 hälsotillståndet för program med HealthState värdet OK (2) och varning (4) returneras.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --events-health-state-filter | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4).  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --exclude-health-statistics | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel. |
| --include-system-application-health-statistics | Anger om hälsostatistik bör innehålla infrastrukturen \: /system hälsostatistik för programmet. FALSKT som standard. Om in IncludeSystemApplicationHealthStatistics har angetts till true, hälsotillståndet statistiken omfattar de entiteter som hör till infrastrukturresursen \: /system-programmet. I annat fall innehåller frågeresultatet hälsostatistik endast för användarnas program. Hälsostatistik måste inkluderas i frågeresultatet för den här parametern som ska användas. |
| --nodes-health-state-filter | Tillåter filtrering av noden hälsotillstånd tillstånd objekten som returneras i resultatet av klustret hälsotillstånd fråga baserat på deras hälsotillstånd. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast de noder som matchar filtret returneras. Alla noder används för att analysera sammanställda hälsotillståndet. Om inte anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som hämtats med hjälp av en Bitvis ”OR”-operator. Till exempel om det angivna värdet är 6 hälsotillståndet för noderna med HealthState värdet OK (2) och varning (4) returneras.  <br> -Standard - standardvärde. Matchar alla HealthState. Värdet är noll.  <br> -Ingen - Filter som inte matchar något värde för HealthState. Använda så att inga resultat returneras på en viss samling av tillstånd. Värdet är 1.  <br> -Filtrera ok - att matchningar indata med HealthState värde Ok. Värdet är 2.  <br> -Varning - Filter som matchar med HealthState indatavärdet varning. Värdet är 4.  <br> -Fel Filter som matchar indata med HealthState värdet fel. Värdet är 8.  <br> -Alla - Filter som matchar indata med ett HealthState-värde. Värdet är 65535. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-manifest"></a>sfctl klustermanifestet
Hämta Service Fabric-klustermanifestet.

Hämta Service Fabric-klustermanifestet. Klustermanifestet innehåller egenskaper för klustret som innehåller olika nodtyper på klustret, säkerhetskonfigurationer, fel, och uppgraderingsdomän topologier, osv. Dessa egenskaper anges som en del av filen ClusterConfig.JSON när du distribuerar ett fristående kluster. Men de flesta av informationen i klustermanifestet genereras internt av service fabric under distributionen av klustret i andra scenarier (t.ex. när du använder Azure-portalen). Innehållet i klustermanifestet är endast i informationssyfte och användare förväntas inte skapa ett beroende på formatet på filinnehållet eller dess tolkning.

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

## <a name="sfctl-cluster-operation-cancel"></a>Avbryt med sfctl klustret igen
Avbryter en egen användaren felåtgärd.

Följande API: er starta fault-åtgärder som kan avbrytas genom att använda CancelOperation\: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Om force är FALSKT, sedan den angivna användare egen åtgärden smidigt stoppas och rensas.  Om force är sant, kommandot kommer att avbrytas och vissa interna tillståndet finnas kvar.  Anger att framtvinga true ska användas med försiktighet. Är inte tillåtet att anropa detta API med force inställd på true tills detta API har redan anropats på samma test-kommando med force inställd på false första, eller testkommandot redan har en OperationState OperationState.RollingBack. 

Tydliggörande\: OperationState.RollingBack innebär att systemet blir/Rensa internt system tillstånd på grund av kommandot.  Det kan inte återställa data om testkommandot var att leda till dataförlust.  Till exempel om du anropar StartDataLoss sedan anropa den här API: et, systemet kommer endast att rensa upp interna tillståndet från att köra kommandot. Den återställer inte target-partitionens data om kommandot stått stilla tillräckligt långt kan orsaka förlust av data. 

> [!NOTE]
> Om detta API har anropats med force == true, intern tillstånd finnas kvar.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärden-id [krävs] | Ett GUID som identifierar ett detta API-anrop.  Detta skickas vidare till motsvarande GetProgress API. |
| --tvinga | Anger om du vill återställa och rensa interna system-tillstånd ändras av användaren egen-åtgärden kan genomföras utan problem. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-operation-list"></a>sfctl kluster åtgärden-list
Hämtar en lista över användare egen fel åtgärder filtreras efter angivna indata.

Hämtar listan över användare egen fel åtgärder filtreras efter angivna indata.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --state-filter | Används för att filtrera på Operationstates för åtgärder som initierats av användaren. <br> 65535 - Markera alla <br> 1 – Välj körs <br> 2 – Välj återställning av <br>8 – Välj slutförd <br>16 – Välj Faulted <br>32 - Välj annullerad <br>64 - Välj ForceCancelled.  <br>Standard\: 65535. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --type-filter | Används för att filtrera på Åtgärdstyp för åtgärder som initierats av användaren. <br> 65535 - Markera alla <br> 1 – Välj PartitionDataLoss. <br> 2 – Välj PartitionQuorumLoss. <br> 4 – Välj PartitionRestart. <br> 8 – Välj NodeTransition.  <br> Standard\: 65535. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-provision"></a>sfctl kluster etablera
Etablera kodning eller paket i ett Service Fabric-kluster.

Validera och etablera kodning eller paket i ett Service Fabric-kluster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kluster-manifest-filsökväg | Klustret manifestfilen sökvägen. |
| --code-file-path | Filsökvägen för klustret kod paketet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-recover-system"></a>sfctl kluster Återställ-system
Anger att den ska försöka att återställa systemtjänster som för närvarande har fastnat i förlorar kvorum till Service Fabric-klustret.

Anger att den ska försöka att återställa systemtjänster som för närvarande har fastnat i förlorar kvorum till Service Fabric-klustret. Den här åtgärden bör endast utföras om det är känt att replikerna är nere inte kan återställas. Felaktig användning av detta API kan orsaka förlust av data.

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

## <a name="sfctl-cluster-report-health"></a>sfctl kluster report-health
Skickar en hälsorapport på Service Fabric-klustret.

Rapporten måste innehålla information om orsaken hälsorapport och egenskapen som har rapporterats. Rapporten skickas till ett Service Fabric gateway-noden, som vidarebefordrar till health store. Rapporten kan accepteras av gatewayen, men avvisats av health store efter extra valideringen. Health store kan till exempel avvisa rapporten på grund av en ogiltig parameter, t.ex. ett sekvensnummer som är inaktuella. Kontrollera att rapporten ska visas i HealthEvents i klustret för att se om rapporten har tillämpats i health store.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --health-egenskapen [krävs] | Egenskapen för hälsoinformation. <br><br> En entitet kan ha rapporter om hälsotillstånd för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkningen för att tillåta rapport flexibiliteten att kategorisera tillstånd-villkor som utlöser rapporten. En rapport med SourceId ”LocalWatchdog” kan till exempel övervaka status för tillgängliga disken på en nod, så att den kan rapportera ”AvailableDisk”-egenskap på noden. Samma rapport kan övervaka nod-anslutningen så att den kan rapportera en egenskap ”anslutning” på samma nod. Dessa rapporter behandlas i health store, som separata health-händelser för den angivna noden. Tillsammans med målentiteten identifierar egenskapen hälsoinformation. |
| --hälsotillståndet [krävs] | Möjliga värden omfattar\: ”ogiltig”, ”Ok”, ”varning”, ”fel”, ”okänd”. |
| --käll-id [krävs] | Källnamn som identifierar/klientsystemet/watchdog-komponenten som genererade hälsoinformation. |
| – Beskrivning | Beskrivning av hälsoinformation. <br><br> Den motsvarar fritext som används för att lägga till mänskliga läsbar information om rapporten. Den maximala stränglängden för beskrivningen är 4 096 tecken. Om strängen inte trunkeras den automatiskt. När trunkeras, de sista tecknen i beskrivningen innehåller en markör ”[trunkerat]” och totala storleken på målsträngen är 4 096 tecken. Förekomst av markören anger för användare att trunkering inträffade. Observera att när trunkeras, beskrivningen har mindre än 4 096 tecken från den ursprungliga strängen. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälsorapport skickas till en Service Fabric gateway programmet, som vidarebefordrar till health store. Om Immediate anges till SANT, skickas rapporten direkt från HTTP-Gateway till health store, oavsett inställningarna i fabric-klient som använder HTTP-Gateway-program. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tidpunkten och andra villkor misslyckas skicka rapporten fortfarande, till exempel om HTTP-Gateway är stängd eller meddelandet når inte gatewayen. Om Immediate är inställd på false, skickas rapporten baserat på klientinställningarna hälsotillstånd från HTTP-Gateway. Det kan därför batchhanteras enligt HealthReportSendInterval-konfigurationen. Det här är den rekommenderade inställningen eftersom den tillåter hälsotillstånd klienten att optimera reporting meddelanden hälsoarkivet, samt för bearbetning av hälsotillstånd. Som standard skickas rapporter inte omedelbart. |
| --remove-when-expired | Värde som anger om rapporten tas bort från health store när den upphör att gälla. <br><br> Om värdet är true, rapporten tas bort från health store när den upphör att gälla. Om värdet är FALSKT, rapporten behandlas som ett fel när den har upphört att gälla. Värdet för den här egenskapen är false som standard. När klienterna rapporterar regelbundet, ska de ange RemoveWhenExpired FALSKT (standard). På så sätt kan är personen har problem (t.ex. deadlock) och kan inte rapportera entiteten utvärderas vid fel när hälsorapporten upphör att gälla. Detta flaggar entiteten som ett felaktigt hälsotillstånd. |
| ---sekvensnummer | Sekvensnumret för den här hälsorapport som en numerisk sträng. <br><br> Sekvensnumret rapporten används av health store för att identifiera inaktuella rapporter. Om inte anges genereras ett sekvensnummer automatiskt av hälsotillstånd klienten när du lägger till en rapport. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --ttl | Den tid som den här hälsorapport är giltig. Det här fältet använder ISO8601-format för att ange varaktigheten. <br><br> När klienterna rapporterar med jämna mellanrum, bör de skicka rapporter med frekvens som är högre än TTL-värde. Om klienterna rapporterar på övergång, kan de ange time to live till obegränsad. När TTL-värde upphör att gälla hälsohändelsen som innehåller hälsoinformation är antingen tas bort från health store om RemoveWhenExpired är true och utvärderas vid fel, om RemoveWhenExpired false. Om inte tidpunkten TTL-värde standardvärdet är oändligt värde. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-select"></a>sfctl klustret väljer
Ansluter till en slutpunkt för Service Fabric-kluster.

Om du ansluter till säkert kluster, kan du ange en absolut sökväg till ett certifikat (.crt) och nyckelfilen (.key) eller en enda fil med både (.pem). Ange inte båda. Du kan också om ansluta till ett säkert kluster kan även ange en absolut sökväg till en CA paket fil eller katalog för betrodda CA-certifikat. Om du använder en katalog med CA-certifikat, `c_rehash <directory>` tillhandahålls av OpenSSL måste köras först för att beräkna certifikatet-hashar och skapa lämpliga symboliska länkar.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --endpoint [krävs] | Slutpunkts-URL, inklusive port och prefixet HTTP eller HTTPS-klustret. |
| --aad | Använd Azure Active Directory för autentisering. |
| --ca | Absolut sökväg till CA-certifikat katalog att behandla som giltiga eller samlingsfil för CA: N. |
| --cert | Absolut sökväg till en klientcertifikatfilen. |
| --nyckel | Absolut sökväg till nyckel för klientcertifikatfilen. |
| --no-Kontrollera | Inaktivera verifiering för certifikat när du använder HTTPS måste du komma ihåg\: det här är ett osäkert alternativ och ska inte användas för produktionsmiljöer. |
| --pem | Absolut sökväg till klientcertifikat, som en PEM-fil. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-show-connection"></a>sfctl kluster show-connection
Visa vilka Service Fabric-kluster som den här sfctl-instansen är ansluten till.

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-unprovision"></a>sfctl kluster avetablera
Avetablera kodning eller paket i ett Service Fabric-kluster.

Det går för att avetablera kod och konfiguration separat.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --code-version | Paketet klusterkodsversion. |
| --config-version | Versionen av klustermanifestet. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-upgrade"></a>sfctl uppgradering av klustret
Börja uppgradera kodning eller version av Service Fabric-kluster.

Verifiera de angivna parametrarna för uppgradering och börja uppgradera kodning eller version av Service Fabric-kluster om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-health-map | JSON-kodad ordlista över par av namn och Maxprocent defekta innan du höjer fel. |
| --app-type-health-map | JSON-kodad ordlista med par med programtypnamn och Maxprocent defekta innan du höjer fel. |
| --code-version | Klusterkodsversion. |
| --config-version | Konfigurationsversion för klustret. |
| --delta-hälsa – utvärdering | Kan delta hälsotillstånd utvärdering i stället för absoluta hälsotillstånd utvärderingen efter slutförandet av varje uppgraderingsdomän. |
| --delta-unhealthy-nodes | Högsta tillåtna procentandel noder hälsotillstånd försämring tillåts vid klusteruppgradering.  Standard\: 10. <br><br> Delta mäts mellan tillståndet i noderna i början av uppgraderingen och tillståndet i noderna vid tidpunkten för health utvärderingen. Kontrollen utförs efter varje uppgradering slutförande uppgraderingsdomän globala tillstånd för klustret är inom tolererat gränser. |
| --åtgärd vid uppgraderingsfel | Möjliga värden omfattar\: ”ogiltig”, ”återställa”, ”manuell”. |
| --force-restart | Processer kernelpaketet startas om under uppgraderingen även när code-versionen inte har ändrats. <br><br> Uppgraderingen ändras bara konfiguration eller data. |
| --health-check-retry | Hur lång tid mellan försök att utföra hälsokontroller om programmet eller klustret inte är felfri. |
| --health-check-stable | Hur lång tid att programmet eller klustret måste vara felfria innan uppgraderingen fortsätter du med nästa uppgraderingsdomän. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --health-check-wait | Hur lång tid att vänta efter att du har slutfört en uppgraderingsdomän innan du startar hälsotillståndet kontrollerar processen. |
| --replica-set-check-timeout | Längsta tid att blockera bearbetning av en uppgraderingsdomän och förhindra förlust av tillgänglighet när det finns ett oväntat problem. <br><br> När den här tidsgränsen upphör kommer att fortsätta bearbetningen av uppgraderingsdomänen oavsett förlust av tillgänglighetsproblem. Tidsgränsen återställs i början av varje uppgraderingsdomän. Giltiga värden är mellan 0 och 42949672925 inkluderande. |
| --rolling-upgrade-mode | Möjliga värden omfattar\: ”ogiltig”, ”UnmonitoredAuto', 'UnmonitoredManual', 'Övervakade'.  Standard\: UnmonitoredAuto. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --unhealthy-applications | Högsta tillåtna procentandel program som är felaktiga innan den rapporterar ett fel. <br><br> Om du vill tillåta 10% av program vara felaktig, blir det här värdet 10. Procentandelen representerar högsta tolererat procentandelen av program som kan vara felaktig innan klustret anses av misstag. Om procentandelen respekteras men det finns minst en felaktigt program, utvärderas hälsotillståndet som varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet instanser av programmet i klustret, exklusive program typer av program som ingår i ApplicationTypeHealthPolicyMap. Beräkningen Avrundar uppåt till tolerera fel på ett fel på små mängder program. |
| --defekta noder | Högsta tillåtna procentandel av defekta noder innan den rapporterar ett fel. <br><br> Om du vill tillåta 10% av noder vara felaktig, blir det här värdet 10. Procentandelen representerar maximalt tolererat procentandel noder som kan vara felaktig innan klustret anses av misstag. Om procentandelen respekteras men det finns minst en felaktig nod, utvärderas hälsotillståndet som varning. I procent beräknas genom att dividera antalet defekta noder över det totala antalet noder i klustret. Beräkningen Avrundar uppåt till tolerera fel på ett fel på små antal noder. I stora kluster kommer vissa noder alltid att ned eller ut för reparationer, så att den här procentandelen ska konfigureras för att kunna hanteras som. |
| --upgrade-domain-delta-unhealthy-nodes | Högsta tillåtna procentandel noder i uppgraderingsdomän hälsotillstånd försämring tillåts vid klusteruppgradering.  Standard\: 15. <br><br> Delta mäts mellan tillståndet för uppgraderingsdomän-noder i början av uppgraderingen och tillståndet för uppgraderingsdomän noderna vid tidpunkten för health utvärderingen. Kontrollen utförs efter varje uppgradering slutförande uppgraderingsdomän för alla slutförts uppgraderingsdomäner att kontrollera att tillståndet för uppgraderingsdomänerna inom tolererat gränser. |
| --upgrade-domain-timeout | Hur lång tid varje domän har slutförts innan FailureAction körs. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --upgrade-timeout | Hur lång tid övergripande uppgraderingen har slutförts innan FailureAction körs. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --warning-as-error | Anger om varningar behandlas med samma allvarlighetsgrad som fel. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl kluster uppgraderingen-återuppta
Kontrollera klusteruppgraderingen gå vidare till nästa uppgraderingsdomän.

Övergå kluster kodning eller uppgradera på nästa uppgraderingsdomän om det är lämpligt.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| – uppgradera domäner [krävs] | Med nästa uppgraderingsdomän för den här uppgradering av klustret. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Global Arguments

|Argument|Beskrivning|
| --- | --- |
| --debug | Öka detaljnivå loggning för att visa alla felsöka loggar. |
| --hjälpa -h | Visa den här hjälpmeddelande och avsluta. |
| --utdata -o | Utdataformat.  Tillåtna värden\: json, jsonc, tabell, TVs.  Standard\: json. |
| – fråga | JMESPath-frågesträng. Se http\://jmespath.org/ för mer information och exempel. |
| --utförlig | Öka detaljnivå för loggning. Använd--felsökning för fullständig felsökningsloggar. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl kluster uppgraderingen-återställning
Återställa uppgradering av Service Fabric-kluster.

Återställa kodning eller uppgradering av Service Fabric-kluster.

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

## <a name="sfctl-cluster-upgrade-status"></a>sfctl uppgraderingen-klusterstatus
Hämtar förloppet för den aktuella uppgraderingen av klustret.

Hämtar den aktuella statusen för pågående klusteruppgraderingen. Om ingen uppgradering pågår just nu, kan du hämta det senaste tillståndet för den tidigare klusteruppgraderingen.

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

## <a name="sfctl-cluster-upgrade-update"></a>sfctl kluster uppgraderingen-uppdatering
Uppdatera Uppgraderingsparametrar för en uppgradering av Service Fabric-klustret.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-health-map | JSON-kodad ordlista över par av namn och Maxprocent defekta innan du höjer fel. |
| --app-type-health-map | JSON-kodad ordlista med par med programtypnamn och Maxprocent defekta innan du höjer fel. |
| --delta-hälsa – utvärdering | Kan delta hälsotillstånd utvärdering i stället för absoluta hälsotillstånd utvärderingen efter slutförandet av varje uppgraderingsdomän. |
| --delta-unhealthy-nodes | Högsta tillåtna procentandel noder hälsotillstånd försämring tillåts vid klusteruppgradering.  Standard\: 10. <br><br> Delta mäts mellan tillståndet i noderna i början av uppgraderingen och tillståndet i noderna vid tidpunkten för health utvärderingen. Kontrollen utförs efter varje uppgradering slutförande uppgraderingsdomän globala tillstånd för klustret är inom tolererat gränser. |
| --åtgärd vid uppgraderingsfel | Möjliga värden omfattar\: ”ogiltig”, ”återställa”, ”manuell”. |
| --force-restart | Processer kernelpaketet startas om under uppgraderingen även när code-versionen inte har ändrats. <br><br> Uppgraderingen ändras bara konfiguration eller data. |
| --health-check-retry | Hur lång tid mellan försök att utföra hälsokontroller om programmet eller klustret inte är felfri. |
| --health-check-stable | Hur lång tid att programmet eller klustret måste vara felfria innan uppgraderingen fortsätter du med nästa uppgraderingsdomän. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --health-check-wait | Hur lång tid att vänta efter att du har slutfört en uppgraderingsdomän innan du startar hälsotillståndet kontrollerar processen. |
| --replica-set-check-timeout | Längsta tid att blockera bearbetning av en uppgraderingsdomän och förhindra förlust av tillgänglighet när det finns ett oväntat problem. <br><br> När den här tidsgränsen upphör kommer att fortsätta bearbetningen av uppgraderingsdomänen oavsett förlust av tillgänglighetsproblem. Tidsgränsen återställs i början av varje uppgraderingsdomän. Giltiga värden är mellan 0 och 42949672925 inkluderande. |
| --rolling-upgrade-mode | Möjliga värden omfattar\: ”ogiltig”, ”UnmonitoredAuto', 'UnmonitoredManual', 'Övervakade'.  Standard\: UnmonitoredAuto. |
| --timeout -t | Tidsgräns för Server på några sekunder.  Standard\: 60. |
| --unhealthy-applications | Högsta tillåtna procentandel program som är felaktiga innan den rapporterar ett fel. <br><br> Om du vill tillåta 10% av program vara felaktig, blir det här värdet 10. Procentandelen representerar högsta tolererat procentandelen av program som kan vara felaktig innan klustret anses av misstag. Om procentandelen respekteras men det finns minst en felaktigt program, utvärderas hälsotillståndet som varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet instanser av programmet i klustret, exklusive program typer av program som ingår i ApplicationTypeHealthPolicyMap. Beräkningen Avrundar uppåt till tolerera fel på ett fel på små mängder program. |
| --defekta noder | Högsta tillåtna procentandel av defekta noder innan den rapporterar ett fel. <br><br> Om du vill tillåta 10% av noder vara felaktig, blir det här värdet 10. Procentandelen representerar maximalt tolererat procentandel noder som kan vara felaktig innan klustret anses av misstag. Om procentandelen respekteras men det finns minst en felaktig nod, utvärderas hälsotillståndet som varning. I procent beräknas genom att dividera antalet defekta noder över det totala antalet noder i klustret. Beräkningen Avrundar uppåt till tolerera fel på ett fel på små antal noder. I stora kluster kommer vissa noder alltid att ned eller ut för reparationer, så att den här procentandelen ska konfigureras för att kunna hanteras som. |
| --upgrade-domain-delta-unhealthy-nodes | Högsta tillåtna procentandel noder i uppgraderingsdomän hälsotillstånd försämring tillåts vid klusteruppgradering.  Standard\: 15. <br><br> Delta mäts mellan tillståndet för uppgraderingsdomän-noder i början av uppgraderingen och tillståndet för uppgraderingsdomän noderna vid tidpunkten för health utvärderingen. Kontrollen utförs efter varje uppgradering slutförande uppgraderingsdomän för alla slutförts uppgraderingsdomäner att kontrollera att tillståndet för uppgraderingsdomänerna inom tolererat gränser. |
| --upgrade-domain-timeout | Hur lång tid varje domän har slutförts innan FailureAction körs. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| – uppgradera typ | Möjliga värden omfattar\: ”ogiltig”, ”löpande”, ”Rolling_ForceRestart”.  Standard\: löpande. |
| --upgrade-timeout | Hur lång tid övergripande uppgraderingen har slutförts innan FailureAction körs. <br><br> Först tolkas det som en sträng som representerar en ISO 8601-varaktighet. Om det misslyckas så tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --warning-as-error | Anger om varningar behandlas med samma allvarlighetsgrad som fel. |

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