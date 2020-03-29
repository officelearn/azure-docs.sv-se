---
title: Lägga till anpassade hälsorapporter för Service Fabric
description: Beskriver hur du skickar anpassade hälsorapporter till Azure Service Fabric-hälsoentiteter. Ger rekommendationer för att utforma och genomföra hälsorapporter av hög kvalitet.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d00f740085b15bdb5fe698a069d97f168507f31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451589"
---
# <a name="add-custom-service-fabric-health-reports"></a>Lägga till anpassade hälsorapporter för Service Fabric
Azure Service Fabric introducerar en [hälsomodell](service-fabric-health-introduction.md) som utformats för att flagga felaktiga kluster- och programvillkor på specifika entiteter. Hälsomodellen använder **hälsoreportrar** (systemkomponenter och vakthundar). Målet är enkel och snabb diagnos och reparation. Service författare måste tänka upfront om hälsa. Alla tillstånd som kan påverka hälsan bör rapporteras, särskilt om det kan hjälpa flagga problem nära roten. Hälsoinformationen kan spara tid och ansträngning på felsökning och undersökning. Nyttan är särskilt tydlig när tjänsten är igång i stor skala i molnet (privat eller Azure).

Service Fabric-reportrar övervakar identifierade villkor av intresse. De rapporterar om dessa villkor på grundval av deras lokala uppfattning. [Hälsoarkivet](service-fabric-health-introduction.md#health-store) sammanställer hälsodata som skickas av alla reportrar för att avgöra om entiteter är globalt felfria. Modellen är avsedd att vara rik, flexibel och enkel att använda. Kvaliteten på hälsorapporterna avgör riktigheten i hälsovyn i klustret. Falska positiva identifieringar som felaktigt visar felaktiga problem kan påverka uppgraderingar eller andra tjänster som använder hälsodata negativt. Exempel på sådana tjänster är reparationstjänster och varningsmekanismer. Därför behövs en del tankar för att tillhandahålla rapporter som fångar upp förhållanden av intresse på bästa möjliga sätt.

För att utforma och genomföra hälsorapportering måste vakthundar och systemkomponenter:

* Definiera villkoret de är intresserade av, hur det övervakas och påverkan på klustret eller programfunktionen. Baserat på den här informationen, besluta om hälsorapporten egendom och hälsotillstånd.
* Bestäm den [entitet](service-fabric-health-introduction.md#health-entities-and-hierarchy) som rapporten gäller för.
* Bestäm var rapporteringen görs, inifrån tjänsten eller från en intern eller extern watchdog.
* Definiera en källa som används för att identifiera reportern.
* Välj en rapporteringsstrategi, antingen regelbundet eller på övergångar. Det rekommenderade sättet är regelbundet, eftersom det kräver enklare kod och är mindre benägna att fel.
* Bestäm hur länge rapporten för ohälsosamma tillstånd ska finnas kvar i hälsoarkivet och hur den ska rensas. Med den här informationen bestämmer du rapportens tid att leva och ta bort-vid-utgångsdatum.Using this information, decide the report's time to live and remove-on-expiration behavior.

Som nämnts kan rapportering göras från:

* Den övervakade service fabric-repliken.
* Interna watchdogs som distribueras som en Service Fabric-tjänst (till exempel en tjänst fabric-tjänst som övervakar villkor och ärenderapporter). Watchdogs kan distribueras en alla noder eller kan affiniteras till den övervakade tjänsten.
* Interna watchdogs som körs på noderna Service Fabric men *som inte* implementeras som Service Fabric-tjänster.
* Externa watchdogs som avsöker resursen *utanför* Service Fabric-klustret (till exempel övervakningstjänsten som Gomez).

> [!NOTE]
> Ur rutan fylls klustret med hälsorapporter som skickas av systemkomponenterna. Läs mer på [Använda systemhälsorapporter för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Användarrapporterna måste skickas på [hälsoentiteter](service-fabric-health-introduction.md#health-entities-and-hierarchy) som redan har skapats av systemet.
> 
> 

När hälsorapporteringsdesignen är klar kan hälsorapporter skickas enkelt. Du kan använda [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) för att rapportera hälsotillstånd om klustret inte är [säkert](service-fabric-cluster-security.md) eller om fabric-klienten har administratörsbehörighet. Rapportering kan göras via API:et med hjälp av [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), via PowerShell eller via REST. Batchrapporter för konfigurationsrattar för förbättrad prestanda.

> [!NOTE]
> Rapporthälsa är synkron och representerar bara valideringsarbetet på klientsidan. Det faktum att rapporten accepteras av `Partition` hälsoklienten eller objekten eller `CodePackageActivationContext` objekten betyder inte att den tillämpas i butiken. Det skickas asynkront och eventuellt batched med andra rapporter. Bearbetningen på servern kan fortfarande misslyckas: sekvensnumret kan vara inaktuellt, den enhet som rapporten måste tillämpas på har tagits bort, etc.
> 
> 

## <a name="health-client"></a>Hälsa klient
Hälsorapporterna skickas till hälsochefen via en hälsoklient, som bor inuti tygklienten. Hälsochefen sparar rapporter i hälsobutiken. Hälsoklienten kan konfigureras med följande inställningar:

* **HealthReportSendInterval**: Fördröjningen mellan den tidpunkt då rapporten läggs till i klienten och den tid den skickas till hälsohanteraren. Används för att batchrapportera till ett enda meddelande i stället för att skicka ett meddelande för varje rapport. Batchbearbetningen förbättrar prestanda. Standard: 30 sekunder.
* **HealthReportRetrySendInterval**: Det intervall med vilket hälsoklienten skickar in ackumulerade hälsorapporter till hälsohanteraren. Standard: 30 sekunder, minst: 1 sekund.
* **HealthOperationTimeout**: Timeout-perioden för ett rapportmeddelande som skickas till hälsohanteraren. Om ett meddelande time out, hälsoklienten försöker det tills hälsohanteraren bekräftar att rapporten har bearbetats. Standard: två minuter.

> [!NOTE]
> När rapporterna är batchade måste fabric-klienten hållas vid liv i minst HealthReportSendInterval för att säkerställa att de skickas. Om meddelandet går förlorat eller om hälsohanteraren inte kan tillämpa dem på grund av tillfälliga fel, måste fabric-klienten hållas vid liv längre för att ge den en chans att försöka igen.
> 
> 

Buffringen på klienten tar hänsyn till rapporternas unika egenskaper. Om till exempel en viss dålig reporter rapporterar 100 rapporter per sekund på samma egenskap för samma entitet, ersätts rapporterna med den senaste versionen. Högst en sådan rapport finns i klientkön. Om batchbearbetning är konfigurerad är antalet rapporter som skickas till hälsohanteraren bara ett per sändningsintervall. Den här rapporten är den senaste tillagda rapporten, som återspeglar den senaste tillståndet för entiteten.
Ange konfigurationsparametrar när `FabricClient` skapas genom att skicka [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) med önskade värden för hälsorelaterade poster.

I följande exempel skapas en fabric-klient och anges att rapporterna ska skickas när de läggs till. Vid timeout och fel som kan göras om sker återförsök var 40:e sekund.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Vi rekommenderar att du behåller standardinställningarna för fabric-klient, som är inställda på `HealthReportSendInterval` 30 sekunder. Den här inställningen säkerställer optimal prestanda på grund av batchbearbetning. För kritiska rapporter som måste skickas så `HealthReportSendOptions` snart `true` som möjligt kan du använda med Omedelbar i [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Omedelbara rapporter kringgå batchintervallet. Använd denna flagga med omsorg; vi vill dra nytta av hälsa klient batching när det är möjligt. Omedelbar sändning är också användbart när fabric-klienten stängs (till exempel har processen fastställt ogiltigt tillstånd och måste stängas av för att förhindra biverkningar). Det säkerställer en bästa möjliga sändning av de ackumulerade rapporterna. När en rapport läggs till med omedelbar flagga batchar hälsoklienten alla ackumulerade rapporter sedan senaste sändningen.

Samma parametrar kan anges när en anslutning till ett kluster skapas via PowerShell. I följande exempel startas en anslutning till ett lokalt kluster:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

På samma sätt som API `-Immediate` kan rapporter skickas med växeln `HealthReportSendInterval` som ska skickas omedelbart, oavsett värdet.

För REST skickas rapporterna till Service Fabric-gatewayen, som har en intern fabric-klient. Som standard är den här klienten konfigurerad för att skicka rapporter som har skickats var 30:e sekund. Du kan ändra batchintervallet med `HttpGatewayHealthReportSendInterval` `HttpGateway`klusterkonfigurationsinställningen på . Som nämnts är ett bättre alternativ `Immediate` att skicka rapporterna med sant. 

> [!NOTE]
> Om du vill vara säker på att obehöriga tjänster inte kan rapportera hälsotillstånd mot entiteterna i klustret konfigurerar du servern så att den bara accepterar begäranden från skyddade klienter. Den `FabricClient` som används för rapportering måste ha aktiverat säkerhet för att kunna kommunicera med klustret (till exempel med Kerberos eller certifikatautentisering). Läs mer om [klustersäkerhet](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Rapport inifrån lågprivilegitjänster
Om Service Fabric-tjänster inte har administratörsåtkomst till klustret kan du `Partition` `CodePackageActivationContext`rapportera hälsotillstånd för entiteter från den aktuella kontexten till eller .

* För tillståndslösa tjänster använder du [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) för att rapportera om den aktuella tjänstinstansen.
* För tillståndskänsliga tjänster använder du [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) för att rapportera om aktuell replik.
* Använd [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) för att rapportera om den aktuella partitionentiteten.
* Använd [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) för att rapportera om aktuellt program.
* Använd [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) för att rapportera om det aktuella programmet som distribueras på den aktuella noden.
* Använd [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) om du vill rapportera om ett servicepaket för programmet som distribueras på den aktuella noden.

> [!NOTE]
> Internt, `Partition` och `CodePackageActivationContext` hålla en hälsa klient konfigurerad med standardinställningar. Som förklaras för [hälsoklienten](service-fabric-report-health.md#health-client)batchas rapporter och skickas på en timer. Föremålen ska hållas vid liv för att få en chans att skicka rapporten.
> 
> 

Du kan `HealthReportSendOptions` ange när `Partition` `CodePackageActivationContext` du skickar rapporter via och hälso-API:er. Om du har kritiska rapporter som måste skickas `HealthReportSendOptions` så `true`snart som möjligt kan du använda med Omedelbar . Omedelbara rapporter kringgå batching intervallet för den interna hälso-klienten. Som tidigare nämnts, använd denna flagga med omsorg; vi vill dra nytta av hälsa klient batching när det är möjligt.

## <a name="design-health-reporting"></a>Design hälsa rapportering
Det första steget för att generera högkvalitativa rapporter är att identifiera de villkor som kan påverka tjänstens hälsa. Alla villkor som kan hjälpa flagga problem i tjänsten eller klustret när den startar - eller ännu bättre, innan ett problem händer - kan potentiellt spara miljarder dollar. Fördelarna inkluderar mindre ner tid, färre natttimmar spenderas undersöka och reparera frågor, och högre kundnöjdhet.

När villkoren har identifierats, watchdog författare måste räkna ut det bästa sättet att övervaka dem för balans mellan overhead och användbarhet. Tänk dig till exempel en tjänst som gör komplexa beräkningar som använder vissa temporära filer på en resurs. En watchdog kan övervaka resursen för att säkerställa att tillräckligt med utrymme är tillgängligt. Det kan lyssna efter meddelanden om fil- eller katalogändringar. Den kan rapportera en varning om ett tröskelvärde i förskott har uppnåtts och rapportera ett fel om aktien är full. På en varning kan ett reparationssystem börja rensa äldre filer på resursen. Vid ett fel kan ett reparationssystem flytta servicerepliken till en annan nod. Observera hur tillstånden beskrivs i hälsotermer: tillståndet för det tillstånd som kan anses vara felfritt (ok) eller ohälsosamt (varning eller fel).

När övervakningsdetaljerna har angetts måste en vakthundsskribent ta reda på hur du implementerar vakthunden. Om villkoren kan fastställas inifrån tjänsten kan vakthunden vara en del av själva övervakade tjänsten. Servicekoden kan till exempel kontrollera resursanvändningen och sedan rapportera varje gång den försöker skriva en fil. Fördelen med detta tillvägagångssätt är att rapporteringen är enkel. Försiktighet måste vidtas för att förhindra att watchdog buggar påverkar tjänstens funktionalitet.

Rapportering inifrån den övervakade tjänsten är inte alltid ett alternativ. En watchdog inom tjänsten kanske inte kan identifiera villkoren. Det kanske inte har logik eller data för att göra fastställandet. Omkostnaderna för övervakningen av förhållandena kan vara höga. Villkoren kanske inte heller är specifika för en tjänst, utan påverkar i stället interaktioner mellan tjänster. Ett annat alternativ är att ha watchdogs i klustret som separata processer. Ordningsvakterna övervakar villkoren och rapporterar, utan att påverka de viktigaste tjänsterna på något sätt. Dessa watchdogs kan till exempel implementeras som tillståndslösa tjänster i samma program, distribueras på alla noder eller på samma noder som tjänsten.

Ibland är en watchdog som körs i klustret inte heller ett alternativ. Om det övervakade villkoret är tjänstens tillgänglighet eller funktionalitet som användarna ser det, är det bäst att ha watchdogs på samma plats som användarklienter. Där kan de testa åtgärderna på samma sätt som användarna anropar dem. Du kan till exempel ha en watchdog som finns utanför klustret, utfärdar begäranden till tjänsten och kontrollerar svarstiden och korrektheten i resultatet. (För en kalkylator tjänst, till exempel, returnerar 2 +2 4 på en rimlig tid?)

När watchdog-informationen har slutförts bör du bestämma ett käll-ID som unikt identifierar det. Om flera watchdogs av samma typ finns i klustret måste de rapportera om olika entiteter eller, om de rapporterar på samma entitet, använda olika käll-ID eller egenskap. På så sätt kan deras rapporter samexistera. Egenskapen för hälsorapporten bör fånga det övervakade tillståndet. (I exemplet ovan kan egenskapen vara **ShareSize**.) Om flera rapporter gäller för samma villkor bör egenskapen innehålla viss dynamisk information som gör att rapporter kan samexistera. Om flera resurser till exempel behöver övervakas kan egenskapsnamnet vara **ShareSize-sharename**.

> [!NOTE]
> Använd *inte* hälsoarkivet för att behålla statusinformation. Endast hälsorelaterad information bör rapporteras som hälsotillstånd, eftersom denna information påverkar hälsoutvärderingen av ett företag. Hälsobutiken har inte utformats som en allmänt bruksbutik. Den använder hälsoutvärderingslogik för att aggregera alla data i hälsotillståndet. Att skicka information som inte är relaterad till hälsotillståndet (som rapporteringsstatus med hälsotillståndet OK) påverkar inte det aggregerade hälsotillståndet, men det kan påverka hälsoarkivets prestanda negativt.
> 
> 

Nästa beslutspunkt är vilken enhet som ska rapporteras om. För det mesta identifierar villkoret tydligt entiteten. Välj den entitet med bästa möjliga granularitet. Om ett villkor påverkar alla repliker i en partition, rapportera på partitionen, inte på tjänsten. Det finns hörnfall där mer eftertanke behövs, dock. Om villkoret påverkar en entitet, till exempel en replik, men önskan är att villkoret ska flaggas för mer än varaktigheten av replikliv, bör det rapporteras på partitionen. Annars rensar hälsoarkivet alla rapporter när repliken tas bort. Watchdog författare måste tänka på livslängden för entiteten och rapporten. Det måste vara tydligt när en rapport ska rensas från en butik (till exempel när ett fel som rapporteras på en entitet inte längre gäller).

Låt oss titta på ett exempel som sätter ihop de punkter jag beskrev. Tänk dig ett Service Fabric-program som består av en huvudtillståndskänslig beständig tjänst och sekundära tillståndslösa tjänster som distribueras på alla noder (en sekundär tjänsttyp för varje typ av aktivitet). Huvudhanteraren har en bearbetningskö som innehåller kommandon som ska köras av sekundärer. Sekundärerna kör inkommande begäranden och skickar tillbaka bekräftelsesignaler. Ett villkor som kan övervakas är längden på huvudbearbetningskön. Om huvudkölängden når ett tröskelvärde rapporteras en varning. Varningen anger att sekundärerna inte kan hantera belastningen. Om kön når den maximala längden och kommandon tas bort, rapporteras ett fel, eftersom tjänsten inte kan återställas. Rapporterna kan finnas på egenskapen **QueueStatus**. Watchdogn bor inne i tjänsten och skickas med jämna mellanrum på huvudrepliken. Tiden att leva är två minuter, och det skickas regelbundet var 30 sekunder. Om den primära går ner rensas rapporten automatiskt från arkivet. Om tjänstrepliken är uppe, men den är låst eller har andra problem, upphör rapporten att gälla i hälsoarkivet. I det här fallet utvärderas entiteten vid fel.

Ett annat villkor som kan övervakas är körningstiden för aktiviteten. Huvudstämningen distribuerar aktiviteter till sekundärerna baserat på aktivitetstypen. Beroende på designen kan bakgrunden söka efter aktivitetsstatus. Det kan också vänta på sekundärer att skicka tillbaka bekräftelse signaler när de är klara. I det andra fallet måste man se till att upptäcka situationer där sekundärer dör eller meddelanden går förlorade. Ett alternativ är att befälhavaren skickar en ping-begäran till samma sekundära, som skickar tillbaka sin status. Om ingen status tas emot anser huvudhanteraren att det är ett fel och schemalägger om aktiviteten. Detta förutsätter att uppgifterna är idempotenta.

Det övervakade villkoret kan översättas som en varning om uppgiften inte utförs inom en viss tid (**t1**, till exempel 10 minuter). Om uppgiften inte slutförs i tid (**t2**, till exempel 20 minuter), kan det övervakade villkoret översättas som fel. Den här rapporteringen kan göras på flera sätt:

* Huvudrepliken rapporterar på sig själv med jämna mellanrum. Du kan ha en egenskap för alla väntande aktiviteter i kön. Om minst en aktivitet tar längre tid är rapportstatusen för egenskapen **PendingTasks** en varning eller ett fel, beroende på vad som är lämpligt. Om det inte finns några väntande aktiviteter eller alla aktiviteter som påbörjats är rapportstatusen OK. Aktiviteterna är beständiga. Om den primära går ner, den nyligen befordrade primära kan fortsätta att rapportera ordentligt.
* En annan watchdog-process (i molnet eller externt) kontrollerar uppgifterna (utifrån, baserat på önskat aktivitetsresultat) för att se om de har slutförts. Om de inte respekterar tröskelvärdena skickas en rapport på huvudtjänsten. En rapport skickas också för varje uppgift som innehåller aktivitetsidentifieraren, till exempel **PendingTask+taskId**. Rapporter bör endast skickas på felaktiga tillstånd. Ställ in tid att leva till några minuter och markera rapporterna som ska tas bort när de upphör att gälla för att säkerställa rensning.
* Den sekundära som kör en aktivitetsrapporter när det tar längre tid än förväntat att köra den. Den rapporterar om tjänstinstansen på egenskapen **PendingTasks**. Rapporten identifierar den tjänstinstans som har problem, men den fångar inte situationen där instansen dör. Rapporterna rensas upp då. Den kan rapportera om den sekundära tjänsten. Om den sekundära slutför uppgiften rensar den sekundära instansen rapporten från arkivet. Rapporten fångar inte upp situationen där bekräftelsemeddelandet går förlorat och uppgiften inte är klar från befälhavarens synvinkel.

Men rapporteringen görs i de fall som beskrivs ovan, rapporterna fångas i ansökan hälsa när hälsa utvärderas.

## <a name="report-periodically-vs-on-transition"></a>Rapportera periodvis kontra om övergången
Genom att använda hälsorapporteringsmodellen kan watchdogs skicka rapporter regelbundet eller om övergångar. Det rekommenderade sättet för watchdog rapportering är regelbundet, eftersom koden är mycket enklare och mindre benägna att fel. Watchdogs måste sträva efter att vara så enkla som möjligt för att undvika buggar som utlöser felaktiga rapporter. Felaktiga *felaktiga* rapporter påverkar hälsoutvärderingar och scenarier baserat på hälsa, inklusive uppgraderingar. Felaktiga *felfria* rapporter döljer problem i klustret, vilket inte är önskvärt.

För periodisk rapportering kan vakthunden implementeras med en timer. Vid en timer-motringning kan watchdog kontrollera tillståndet och skicka en rapport baserat på det aktuella tillståndet. Det finns ingen anledning att se vilken rapport som skickades tidigare eller göra några optimeringar när det gäller meddelanden. Hälsoklienten har batchlogik för att hjälpa till med prestanda. Hälsoklienten hålls vid liv, men den försöker igen internt tills rapporten bekräftas av hälsoarkivet eller watchdog genererar en nyare rapport med samma entitet, egenskap och källa.

Rapportering om övergångar kräver noggrann hantering av tillstånd. Watchdog övervakar vissa villkor och rapporter endast när villkoren ändras. Fördelen med detta tillvägagångssätt är att det behövs färre rapporter. Nackdelen är att logiken i vakthund är komplex. Watchdog måste upprätthålla villkoren eller rapporterna, så att de kan inspekteras för att fastställa tillståndsändringar. Vid redundans måste man vara försiktig med rapporter tillagda, men ännu inte skickat till hälsobutiken. Sekvensnumret måste ständigt öka. Om inte, avvisas rapporterna som inaktuella. I de sällsynta fall där dataförlust uppstår kan synkronisering behövas mellan reporterns tillstånd och hälsoarkivets tillstånd.

Rapportering om övergångar är meningsfullt för `Partition` tjänster `CodePackageActivationContext`som rapporterar om sig själva, genom eller . När det lokala objektet (replik- eller distribuerat tjänstpaket/distribuerat program) tas bort tas även alla dess rapporter bort. Den här automatiska rensningen gör att behovet av synkronisering mellan reporter och hälsobutik. Om rapporten är för överordnad partition eller överordnat program måste försiktighet iakttas vid redundans för att undvika inaktuella rapporter i hälsoarkivet. Logik måste läggas till för att upprätthålla rätt tillstånd och rensa rapporten från arkivet när den inte behövs längre.

## <a name="implement-health-reporting"></a>Implementera hälsorapportering
När entiteten och rapportinformationen är klar kan skicka hälsorapporter göras via API, PowerShell eller REST.

### <a name="api"></a>API
Om du vill rapportera via API:et måste du skapa en hälsorapport som är specifik för den entitetstyp som de vill rapportera om. Ge rapporten till en hälsoklient. Alternativt kan du skapa en hälsoinformation och `Partition` skicka `CodePackageActivationContext` den till korrekta rapporteringsmetoder på eller för att rapportera om aktuella entiteter.

I följande exempel visas periodisk rapportering från en watchdog i klustret. Watchdog kontrollerar om en extern resurs kan nås från en nod. Resursen behövs av ett tjänstmanifest i programmet. Om resursen inte är tillgänglig kan de andra tjänsterna i programmet fortfarande fungera korrekt. Därför skickas rapporten på den distribuerade tjänstpaketentiteten var 30:e sekund.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Skicka hälsorapporter med **Send-ServiceFabric*EntityType*HealthReport**.

I följande exempel visas periodisk rapportering om CPU-värden på en nod. Rapporterna ska skickas var 30:e sekund, och de har tid att leva på två minuter. Om de upphör att gälla har reportern problem, så noden utvärderas vid fel. När processorn är över ett tröskelvärde har rapporten ett hälsotillstånd för varning. När processorn ligger över ett tröskelvärde för mer än den konfigurerade tiden rapporteras den som ett fel. Annars skickar reportern ett hälsotillstånd av OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

I följande exempel rapporterars en tillfällig varning på en replik. Det får först partitions-ID och sedan replik-ID för tjänsten den är intresserad av. Den skickar sedan en rapport från **PowershellWatcher** på egenskapen **ResourceDependency**. Rapporten är av intresse i bara två minuter och tas bort från arkivet automatiskt.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Skicka hälsorapporter med REST med POST-begäranden som går till önskad entitet och har i brödtexten hälsorapportbeskrivningen. Se till exempel hur du skickar [REST-klusterhälsorapporter](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) eller [hälsorapporter för tjänsten](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Alla entiteter stöds.

## <a name="next-steps"></a>Nästa steg
Baserat på hälsodata kan tjänstförfattare och kluster-/programadministratörer komma på olika sätt att använda informationen. De kan till exempel ställa in aviseringar baserat på hälsostatus för att fånga allvarliga problem innan de framkallar avbrott. Administratörer kan också ställa in reparationssystem för att åtgärda problem automatiskt.

[Introduktion till hälsoövervakning av service fabric](service-fabric-health-introduction.md)

[Visa hälsorapporter för Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Så här rapporterar och kontrollerar du tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Använda systemhälsorapporter för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)

