---
title: Azure Service Fabric CLI – sfctl-kluster | Microsoft Docs
description: Beskriver kluster kommandona för Service Fabric CLI-sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 305b1e11841dd2da4aa6c0bdeb3df2c76addad87
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036514"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Välj, hantera och använda Service Fabric kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| kod versioner | Hämtar en lista över infrastruktur kod versioner som är etablerade i ett Service Fabric kluster. |
| config-versions | Hämtar en lista över infrastruktur konfigurations versioner som är etablerade i ett Service Fabric kluster. |
| health | Hämtar hälsan för ett Service Fabric-kluster. |
| manifest | Hämta Service Fabric kluster manifestet. |
| åtgärd-Avbryt | Avbryter en användardefinierad fel åtgärd. |
| åtgärds lista | Hämtar en lista över användardefinierade fel åtgärder filtrerade genom angivna indata. |
| provision | Etablera kod eller konfigurations paket för ett Service Fabric-kluster. |
| recover-system | Anger Service Fabric kluster som det ska försöka återställa system tjänster som för närvarande fastnar i kvorum. |
| report-health | Skickar en hälso rapport i Service Fabric klustret. |
| välj | Ansluter till en Service Fabric kluster slut punkt. |
| Visa anslutning | Visa vilka Service Fabric-kluster som den här sfctl-instansen är ansluten till. |
| unprovision | Avetablera kod eller konfigurations paket för ett Service Fabric-kluster. |
| upgrade | Börja uppgradera koden eller konfigurations versionen av ett Service Fabric-kluster. |
| upgrade-resume | Gör så att kluster uppgraderingen går vidare till nästa uppgraderings domän. |
| upgrade-rollback | Återställa uppgraderingen av ett Service Fabric-kluster. |
| upgrade-status | Hämtar förloppet för den aktuella kluster uppgraderingen. |
| uppgradera – uppdatera | Uppdatera uppgraderings parametrarna för en Service Fabric kluster uppgradering. |

## <a name="sfctl-cluster-code-versions"></a>sfctl-kluster kod-versioner
Hämtar en lista över infrastruktur kod versioner som är etablerade i ett Service Fabric kluster.

Hämtar en lista med information om infrastruktur resurs kod versioner som är etablerade i klustret. Parametern CodeVersion kan användas för att alternativt filtrera utdata till endast den aktuella versionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --code-version | Produkt versionen av Service Fabric. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-config-versions"></a>sfctl-kluster konfiguration – versioner
Hämtar en lista över infrastruktur konfigurations versioner som är etablerade i ett Service Fabric kluster.

Hämtar en lista med information om Fabric config-versioner som är etablerade i klustret. Parametern ConfigVersion kan användas för att alternativt filtrera utdata till endast den aktuella versionen.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --config-version | Konfigurations versionen av Service Fabric. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-health"></a>sfctl kluster hälsa
Hämtar hälsan för ett Service Fabric-kluster.

Använd EventsHealthStateFilter för att filtrera samlingen hälso tillstånds händelser som rapporteras i klustret utifrån hälso tillståndet. På samma sätt använder du NodesHealthStateFilter och ApplicationsHealthStateFilter för att filtrera samlingen av noder och program som returneras utifrån deras sammanlagda hälso tillstånd.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --applications-health-state-filter | Tillåter filtrering av de program hälso tillstånds objekt som returneras i resultatet av en kluster hälso fråga baserat på deras hälso tillstånd. Möjliga värden för den här parametern är ett heltals värde som hämtats från medlemmar eller bitvisa åtgärder för medlemmar i HealthStateFilter-uppräkning. Endast program som matchar filtret returneras. Alla program används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av de här värdena som erhålls med hjälp av bitvis or-operator. Om det tillhandahållna värdet till exempel är 6 returneras hälso tillståndet för program med hälso tillstånds värdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --events-health-state-filter | Tillåter filtrering av samlingen av HealthEvent-objekt som returneras baserat på hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast händelser som matchar filtret returneras. Alla händelser används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av dessa värden, erhållna med hjälp av den bitvisa operatorn eller. Om det angivna värdet till exempel är 6 returneras alla händelser med hälso tillstånd svärdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --Exkludera-hälso statistik | Anger om hälso statistik ska returneras som en del av frågeresultatet. Falskt som standard. Statistiken visar antalet underordnade entiteter i hälso tillståndet OK, varning och fel. |
| --include-system-application-health-statistics | Anger om hälso statistik ska innehålla/system för infrastruktur\:resurser. Falskt som standard. Om IncludeSystemApplicationHealthStatistics är inställt på Sant inkluderar hälso statistiken de entiteter som hör till Fabric\:/system-programmet. Annars innehåller frågeresultatet endast hälso statistik för användar program. Hälso statistiken måste inkluderas i frågeresultatet för att den här parametern ska tillämpas. |
| --nodes-health-state-filter | Tillåter filtrering av de nodens hälso tillstånds objekt som returneras i resultatet av en kluster hälso fråga baserat på deras hälso tillstånd. De möjliga värdena för den här parametern är heltals värde för något av följande hälso tillstånd. Endast noder som matchar filtret returneras. Alla noder används för att utvärdera det sammanlagda hälso tillståndet. Om inget anges returneras alla poster. Tillstånds värden är flaggning-baserad uppräkning, så värdet kan vara en kombination av de här värdena som erhålls med hjälp av bitvis or-operator. Om det tillhandahållna värdet till exempel är 6 returneras hälso tillståndet för noder med hälso tillstånds värdet OK (2) och varning (4).  <br> -Standard-standardvärdet. Matchar alla hälso tillstånd. Värdet är noll.  <br> -Inget – filter som inte matchar något värde för hälso tillstånd. Används för att returnera inga resultat för en specifik samling av tillstånd. Värdet är 1.  <br> – OK-filter som matchar inmatade hälso tillstånds värden OK. Värdet är 2.  <br> -Varnings filter som matchar inmatade värde varningar för hälso tillstånd. Värdet är 4.  <br> -Fel-filter som matchar InInformationen med hälso tillstånds värde fel. Värdet är 8.  <br> – Alla – filter som matchar indatamängden med ett värde för hälso tillstånd. Värdet är 65535. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-manifest"></a>sfctl-kluster manifest
Hämta Service Fabric kluster manifestet.

Hämta Service Fabric kluster manifestet. Kluster manifestet innehåller egenskaper för klustret som innehåller olika nodtyper i klustret, säkerhetskonfigurationer, fel-och uppgraderings domänens topologier osv. Dessa egenskaper anges som en del av ClusterConfig. JSON-filen samtidigt som du distribuerar ett fristående kluster. Men merparten av informationen i kluster manifestet genereras internt av Service Fabric under kluster distribution i andra distributions scenarier (t. ex. När du använder Azure Portal). Innehållet i kluster manifestet används endast i informations syfte och användare förväntas inte ta ett beroende på formatet på fil innehållet eller dess tolkning.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl kluster åtgärd-Avbryt
Avbryter en användardefinierad fel åtgärd.

Följande API: er startar fel åtgärder som kan avbrytas med hjälp\: av CancelOperation StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Om tvinga är falskt stoppas och rensas den angivna användardefinierade åtgärden på ett smidigt sätt.  Om tvinga är sant avbryts kommandot och ett internt tillstånd kan vara kvar bakom.  Att ange Force som sant bör användas med försiktighet. Anrop till det här API: t med värdet True är inte tillåtet förrän denna API redan har anropats för samma test kommando med tvinga set till false först, eller om test kommandot redan har en OperationState av OperationState. RollingBack. 

Klargörande\: OperationState. RollingBack innebär att systemet kommer att rensas genom att rensa det interna system tillståndet, vilket orsakas av att kommandot körs.  Det kommer inte att återställa data om test kommandot var för att leda till data förlust.  Om du t. ex. anropar StartDataLoss anropar det här API: t så rensar systemet bara internt tillstånd från att köra kommandot. Den kommer inte att återställa mål partitionens data, om kommandot är tillräckligt långt för att orsaka data förlust. 

> [!NOTE]
> Om detta API anropas med Force = = true kan det interna läget vara kvar bakom.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --åtgärds-ID [obligatoriskt] | Ett GUID som identifierar ett anrop till detta API.  Detta skickas till motsvarande GetProgress-API. |
| --Force | Indikerar om du vill återställa och rensa internt system tillstånd på ett korrekt sätt genom att köra den användardefinierade åtgärden. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-operation-list"></a>sfctl kluster åtgärd – lista
Hämtar en lista över användardefinierade fel åtgärder filtrerade genom angivna indata.

Hämtar listan över användardefinierade fel åtgärder filtrerade genom angivna indata.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --state-filter | Används för att filtrera på OperationState för användardefinierade åtgärder. <br> 65535-Välj alla <br> 1-Välj igång <br> 2-Välj RollingBack <br>8-Välj slutförd <br>16-Välj fel <br>32-Välj avbruten <br>64-Välj ForceCancelled.  <br>Standard\: 65535. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |
| --type-filter | Används för att filtrera på OperationType för användardefinierade åtgärder. <br> 65535-Välj alla <br> 1-Välj PartitionDataLoss. <br> 2-Välj PartitionQuorumLoss. <br> 4-Välj PartitionRestart. <br> 8-Välj NodeTransition.  <br> Standard\: 65535. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-provision"></a>etablera sfctl-kluster
Etablera kod eller konfigurations paket för ett Service Fabric-kluster.

Verifiera och etablera kod eller konfigurations paket för ett Service Fabric-kluster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --kluster-manifest-File-Path | Sökvägen till klustrets manifest fil. |
| --kod-fil-sökväg | Sökväg till kluster filens paket fil. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-recover-system"></a>sfctl-kluster återställning-system
Anger Service Fabric kluster som det ska försöka återställa system tjänster som för närvarande fastnar i kvorum.

Anger Service Fabric kluster som det ska försöka återställa system tjänster som för närvarande fastnar i kvorum. Den här åtgärden bör endast utföras om det är känt att de repliker som är nere inte kan återställas. Felaktig användning av detta API kan orsaka potentiell data förlust.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-report-health"></a>sfctl-kluster rapport – hälsa
Skickar en hälso rapport i Service Fabric klustret.

Rapporten måste innehålla information om källan till hälso rapporten och egenskapen som den rapporteras om. Rapporten skickas till en Service Fabric Gateway-nod som vidarebefordrar till hälso lagret. Rapporten kan godkännas av gatewayen, men avvisas av hälso lagret efter extra verifiering. Hälso lagret kan till exempel avvisa rapporten på grund av en ogiltig parameter, t. ex. ett inaktuellt ordnings nummer. Om du vill se om rapporten har använts i hälso lagret kontrollerar du att rapporten visas i HealthEvents i klustret.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Health-Property [required] | Hälso informationens egenskaper. <br><br> En entitet kan ha hälso rapporter för olika egenskaper. Egenskapen är en sträng och inte en fast uppräkning som tillåter rapportörens flexibilitet att kategorisera det tillstånds villkor som utlöser rapporten. Till exempel kan en rapportör med SourceId "LocalWatchdog" övervaka statusen för den tillgängliga disken på en nod, så att den kan rapportera egenskapen "AvailableDisk" på noden. Samma rapportör kan övervaka nodens anslutning, så att den kan rapportera en egenskap "anslutning" på samma nod. I hälso lagret behandlas dessa rapporter som separata hälso händelser för den angivna noden. Tillsammans med värdet för SourceId kan egenskapen unikt identifiera hälso informationen. |
| --hälso tillstånd [krävs] | Möjliga värden är\: "ogiltig", "OK", "varning", "Error", "okänd". |
| --Käll-ID [obligatoriskt] | Det käll namn som identifierar klient/övervaknings-/system komponenten som genererade hälso informationen. |
| --Beskrivning | Beskrivning av hälso informationen. <br><br> Den representerar fritext som används för att lägga till läsbar information om rapporten. Den maximala sträng längden för beskrivningen är 4096 tecken. Om den angivna strängen blir längre trunkeras den automatiskt. Vid trunkering innehåller de sista tecknen i beskrivningen en markör, "[trunkerad]" och den totala sträng storleken är 4096 tecken. Förekomsten av markören anger för användare som har trunkerats. Observera att beskrivningen innehåller färre än 4096 tecken från den ursprungliga strängen när den trunkeras. |
| --omedelbar | En flagga som anger om rapporten ska skickas omedelbart. <br><br> En hälso rapport skickas till ett Service Fabric Gateway-program, som vidarebefordrar till hälso lagret. Om omedelbar är inställt på Sant skickas rapporten omedelbart från HTTP-gatewayen till hälso lagret, oavsett vilka klient inställningar för klient program varan som HTTP-gatewayen använder. Detta är användbart för kritiska rapporter som ska skickas så snart som möjligt. Beroende på tids inställningar och andra villkor kan det hända att det fortfarande inte går att skicka rapporten, till exempel om HTTP-gatewayen är stängd eller om meddelandet inte når gatewayen. Om omedelbar är inställt på false skickas rapporten baserat på hälso klient inställningarna från HTTP-gatewayen. Därför kommer den att grupperas enligt HealthReportSendInterval-konfigurationen. Detta är den rekommenderade inställningen eftersom den gör det möjligt för hälso klienten att optimera hälso rapporterings meddelanden till hälso Arkiv och bearbetning av hälso rapporter. Som standard skickas inte rapporter direkt. |
| --remove-when-expired | Värde som anger om rapporten tas bort från hälso arkivet när den upphör att gälla. <br><br> Om värdet är True tas rapporten bort från hälso arkivet när den har gått ut. Om värdet är false behandlas rapporten som ett fel när den upphör att gälla. Värdet för den här egenskapen är falskt som standard. När klienter rapporterar regelbundet ska de ange RemoveWhenExpired false (standard). På så sätt har rapportören problem (t. ex. död läge) och kan inte rapportera. enheten utvärderas vid fel när hälso rapporten upphör att gälla. Den här flaggan anger att entiteten har fel hälso tillstånd. |
| --sekvens-nummer | Serie numret för den här hälso rapporten som en numerisk sträng. <br><br> Rapportens sekvensnummer används av hälso lagret för att identifiera inaktuella rapporter. Om inget värde anges genereras ett sekvensnummer automatiskt av hälso klienten när en rapport läggs till. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |
| --TTL | Varaktigheten för vilken den här hälso rapporten är giltig. I det här fältet används ISO8601-format för att ange varaktighet. <br><br> När klienter rapporterar regelbundet bör de skicka rapporter med högre frekvens än tid till Live. Om klienterna rapporterar över över gången kan de ställa in tiden till oändligt. När TTL-tiden förfaller, tas den hälso händelse som innehåller hälso informationen antingen bort från hälso lagret, om RemoveWhenExpired är sant eller om den utvärderas som fel, om RemoveWhenExpired false. Om inget värde anges, är Time to Live standardvärdet oändligt. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-select"></a>Välj sfctl-kluster
Ansluter till en Service Fabric kluster slut punkt.

Om du ansluter till ett säkert kluster anger du en absolut sökväg till ett certifikat (. CRT) och en nyckel fil (. Key) eller en enskild fil med båda (. pem). Ange inte båda. Om du vill ansluta till ett säkert kluster kan du även ange en absolut sökväg till en CA-Bundle eller en katalog över betrodda CA-certifikat. Om du använder en katalog med ca- `c_rehash <directory>` certifikat som tillhandahålls av OpenSSL måste köras först för att beräkna certifikat-hashar och skapa lämpliga symboliska länkar.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --slut punkt [krävs] | URL för kluster slut punkt, inklusive port-och HTTP-eller HTTPS-prefix. |
| --AAD | Använd Azure Active Directory för autentisering. |
| --ca | Absolut sökväg till katalogen CA-certifikat för att behandla som giltig eller CA-paketfil. |
| --cert | Absolut sökväg till en klient certifikat fil. |
| --nyckel | Absolut sökväg till nyckel filen för klient certifikatet. |
| --ingen-verifiera | Inaktivera verifiering av certifikat när du använder https,\: Observera att detta är ett osäkert alternativ och bör inte användas för produktions miljöer. |
| --pem | Absolut sökväg till klient certifikat som en. PEM-fil. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-show-connection"></a>sfctl-kluster Visa-anslutning
Visa vilka Service Fabric-kluster som den här sfctl-instansen är ansluten till.

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-unprovision"></a>sfctl-kluster avetablera
Avetablera kod eller konfigurations paket för ett Service Fabric-kluster.

Det finns stöd för att avetablera kod och konfiguration separat.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --code-version | Paket version för kluster kod. |
| --config-version | Kluster Manifestets version. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-upgrade"></a>uppgradering av sfctl-kluster
Börja uppgradera koden eller konfigurations versionen av ett Service Fabric-kluster.

Verifiera de angivna uppgraderings parametrarna och börja uppgradera koden eller konfigurations versionen av ett Service Fabric kluster om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-Health-Map | JSON-kodad ord lista med par av program namn och maximal procent andel är inte felfri innan fel uppstår. |
| --app-Type-Health-Map | JSON-kodad ord lista med par av program typ namn och maximal procent andel är inte felfri innan fel uppstår. |
| --code-version | Kluster kod version. |
| --config-version | Kluster konfigurations versionen. |
| --delta – hälso utvärdering | Möjliggör delta hälso utvärdering i stället för absolut hälso utvärdering när varje uppgraderings domän har slutförts. |
| --delta-ej felfri-noder | Den högsta tillåtna procent andelen av noders hälso försämring som tillåts under kluster uppgraderingar.  Standard\: 10. <br><br> Delta mätas mellan tillståndet för noderna i början av uppgraderingen och nodernas status vid tidpunkten för hälso utvärderingen. Kontrollen utförs när uppgraderingen av uppgraderings domänen har slutförts för att kontrol lera att klustrets globala tillstånd ligger inom de begränsningar som tolereras. |
| --Failure-åtgärd | Möjliga värden är\: "ogiltig", "rollback", "Manual". |
| --force-restart | Processerna startas om under uppgraderingen även när kod versionen inte har ändrats. <br><br> Uppgraderingen ändrar bara konfiguration eller data. |
| --health-check-retry | Hur lång tid det tar mellan försök att utföra hälso kontroller om programmet eller klustret inte är felfritt. |
| --hälso kontroll – stabil | Hur lång tid programmet eller klustret måste vara felfritt innan uppgraderingen fortsätter till nästa uppgraderings domän. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälso kontroll-vänta | Vänte tiden när en uppgraderings domän har slutförts innan hälso kontrollerna påbörjas. |
| --replica-set-check-timeout | Maximal tid det tar att blockera bearbetningen av en uppgraderings domän och förhindra tillgänglighet när det uppstår oväntade problem. <br><br> När tids gränsen går ut fortsätter bearbetningen av uppgraderings domänen oavsett problem med tillgänglighets förlust. Tids gränsen återställs i början av varje uppgraderings domän. Giltiga värden är mellan 0 och 42949672925. |
| --rolling-upgrade-mode | Möjliga värden är\: "ogiltig", "UnmonitoredAuto", "UnmonitoredManual", "övervakad".  Standard\: UnmonitoredAuto. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |
| --ej felfri-program | Högsta tillåtna procent andel felaktiga program innan ett fel rapporteras. <br><br> Om du till exempel vill att 10% av programmen ska vara felaktiga, skulle värdet vara 10. Procent andelen visar den maximala procent andelen program som kan vara felfria innan klustret betraktas som ett fel. Om procent andelen respekteras men det finns minst ett ohälsosamt program, utvärderas hälsan som varning. Detta beräknas genom att antalet felaktiga program divideras över det totala antalet program instanser i klustret, exklusive program av program typer som ingår i ApplicationTypeHealthPolicyMap. Beräkningen avrundar upp till att tolerera ett problem med ett litet antal program. |
| --ej felfri-noder | Högsta tillåtna procent andel felaktiga noder innan ett fel rapporteras. <br><br> Om du till exempel vill att 10% av noderna ska vara felaktiga, skulle värdet vara 10. Procent andelen visar den maximala procent andelen av noder som kan vara felfria innan klustret betraktas som ett fel. Om procent andelen respekteras men det finns minst en ohälsosam nod, utvärderas hälsan som varning. Procent andelen beräknas genom att antalet felaktiga noder divideras med det totala antalet noder i klustret. Beräkningen avrundar upp till att tolerera ett problem på ett litet antal noder. I stora kluster är vissa noder alltid otillgängliga för reparationer, så den här procent andelen bör konfigureras för att tolerera. |
| --upgrade-domain-delta-unhealthy-nodes | Den högsta tillåtna procent andelen uppgraderingar av hälso tillstånd för noder som tillåts under kluster uppgraderingar.  Standard\: 15. <br><br> Delta mäts mellan tillståndet för noderna i uppgraderings domänen i början av uppgraderingen och tillståndet för uppgraderings domänens noder vid hälso utvärderingen. Kontrollen utförs när varje uppgraderings domän har slutförts för alla slutförda uppgraderings domäner för att kontrol lera att status för uppgraderings domänerna är inom begränsnings gränser. |
| --uppgradering-Domain-timeout | Hur lång tid varje uppgraderings domän måste vara slutförd innan FailureAction körs. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --uppgraderings-timeout | Den tid som den övergripande uppgraderingen måste slutföras innan FailureAction körs. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --warning-as-error | Anger om varningar behandlas med samma allvarlighets grad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl-kluster uppgradering – återuppta
Gör så att kluster uppgraderingen går vidare till nästa uppgraderings domän.

Gör så att kluster koden eller konfigurations uppgraderingen går vidare till nästa uppgraderings domän om det behövs.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --uppgradering – domän [obligatoriskt] | Nästa uppgraderings domän för den här kluster uppgraderingen. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl-kluster uppgradering-återställning
Återställa uppgraderingen av ett Service Fabric-kluster.

Återställa koden eller konfigurations uppgraderingen av ett Service Fabric kluster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl-kluster uppgradering-status
Hämtar förloppet för den aktuella kluster uppgraderingen.

Hämtar det aktuella förloppet för den pågående kluster uppgraderingen. Om ingen uppgradering pågår för närvarande, hämtar du det sista steget i föregående kluster uppgradering.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl-kluster uppgradering – uppdatera
Uppdatera uppgraderings parametrarna för en Service Fabric kluster uppgradering.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --app-Health-Map | JSON-kodad ord lista med par av program namn och maximal procent andel är inte felfri innan fel uppstår. |
| --app-Type-Health-Map | JSON-kodad ord lista med par av program typ namn och maximal procent andel är inte felfri innan fel uppstår. |
| --delta – hälso utvärdering | Möjliggör delta hälso utvärdering i stället för absolut hälso utvärdering när varje uppgraderings domän har slutförts. |
| --delta-ej felfri-noder | Den högsta tillåtna procent andelen av noders hälso försämring som tillåts under kluster uppgraderingar.  Standard\: 10. <br><br> Delta mätas mellan tillståndet för noderna i början av uppgraderingen och nodernas status vid tidpunkten för hälso utvärderingen. Kontrollen utförs när uppgraderingen av uppgraderings domänen har slutförts för att kontrol lera att klustrets globala tillstånd ligger inom de begränsningar som tolereras. |
| --Failure-åtgärd | Möjliga värden är\: "ogiltig", "rollback", "Manual". |
| --force-restart | Processerna startas om under uppgraderingen även när kod versionen inte har ändrats. <br><br> Uppgraderingen ändrar bara konfiguration eller data. |
| --health-check-retry | Hur lång tid det tar mellan försök att utföra hälso kontroller om programmet eller klustret inte är felfritt. |
| --hälso kontroll – stabil | Hur lång tid programmet eller klustret måste vara felfritt innan uppgraderingen fortsätter till nästa uppgraderings domän. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälso kontroll-vänta | Vänte tiden när en uppgraderings domän har slutförts innan hälso kontrollerna påbörjas. |
| --replica-set-check-timeout | Maximal tid det tar att blockera bearbetningen av en uppgraderings domän och förhindra tillgänglighet när det uppstår oväntade problem. <br><br> När tids gränsen går ut fortsätter bearbetningen av uppgraderings domänen oavsett problem med tillgänglighets förlust. Tids gränsen återställs i början av varje uppgraderings domän. Giltiga värden är mellan 0 och 42949672925. |
| --rolling-upgrade-mode | Möjliga värden är\: "ogiltig", "UnmonitoredAuto", "UnmonitoredManual", "övervakad".  Standard\: UnmonitoredAuto. |
| --timeout-t | Server-timeout på några sekunder.  Standard\: 60. |
| --ej felfri-program | Högsta tillåtna procent andel felaktiga program innan ett fel rapporteras. <br><br> Om du till exempel vill att 10% av programmen ska vara felaktiga, skulle värdet vara 10. Procent andelen visar den maximala procent andelen program som kan vara felfria innan klustret betraktas som ett fel. Om procent andelen respekteras men det finns minst ett ohälsosamt program, utvärderas hälsan som varning. Detta beräknas genom att antalet felaktiga program divideras över det totala antalet program instanser i klustret, exklusive program av program typer som ingår i ApplicationTypeHealthPolicyMap. Beräkningen avrundar upp till att tolerera ett problem med ett litet antal program. |
| --ej felfri-noder | Högsta tillåtna procent andel felaktiga noder innan ett fel rapporteras. <br><br> Om du till exempel vill att 10% av noderna ska vara felaktiga, skulle värdet vara 10. Procent andelen visar den maximala procent andelen av noder som kan vara felfria innan klustret betraktas som ett fel. Om procent andelen respekteras men det finns minst en ohälsosam nod, utvärderas hälsan som varning. Procent andelen beräknas genom att antalet felaktiga noder divideras med det totala antalet noder i klustret. Beräkningen avrundar upp till att tolerera ett problem på ett litet antal noder. I stora kluster är vissa noder alltid otillgängliga för reparationer, så den här procent andelen bör konfigureras för att tolerera. |
| --upgrade-domain-delta-unhealthy-nodes | Den högsta tillåtna procent andelen uppgraderingar av hälso tillstånd för noder som tillåts under kluster uppgraderingar.  Standard\: 15. <br><br> Delta mäts mellan tillståndet för noderna i uppgraderings domänen i början av uppgraderingen och tillståndet för uppgraderings domänens noder vid hälso utvärderingen. Kontrollen utförs när varje uppgraderings domän har slutförts för alla slutförda uppgraderings domäner för att kontrol lera att status för uppgraderings domänerna är inom begränsnings gränser. |
| --uppgradering-Domain-timeout | Hur lång tid varje uppgraderings domän måste vara slutförd innan FailureAction körs. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --uppgraderings typ | Möjliga värden är\: "ogiltig", "rullande", "Rolling_ForceRestart".  Standard\: rullande. |
| --uppgraderings-timeout | Den tid som den övergripande uppgraderingen måste slutföras innan FailureAction körs. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --warning-as-error | Anger om varningar behandlas med samma allvarlighets grad som fel. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: -JSON. |
| --fråga | Frågesträngen JMESPath. Se http\://jmespath.org/för mer information och exempel. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).