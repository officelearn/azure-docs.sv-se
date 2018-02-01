---
title: "Lägg till anpassad Service Fabric-hälsorapporter | Microsoft Docs"
description: "Beskriver hur du skickar anpassade hälsorapporter till Azure Service Fabric hälsa entiteter. Ger rekommendationer för att utforma och implementera kvalitet hälsorapporter."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: oanapl
ms.openlocfilehash: 88d80271e744d6f00afd1ff1c3df29180565b59e
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="add-custom-service-fabric-health-reports"></a>Lägga till anpassade hälsorapporter i Service Fabric
Azure Service Fabric introducerar en [hälsomodell](service-fabric-health-introduction.md) utformats för att flaggan ohälsosamt klustret och villkoren för programmet på specifika enheter. Hälsomodell använder **hälsa rapportörer** (systemkomponenter och watchdogs). Målet är enkelt och snabbt diagnos och reparation. Service-skrivare måste du tänka igenom summa om hälsotillstånd. Eventuella villkor som kan påverka hälsa redovisas, särskilt om det kan hjälpa flaggan problem nära roten. Information om hälsa kan spara tid och pengar på felsökning och undersökning. Användbarhet är särskilt tydligt när tjänsten är igång och körs i skala i molnet (privat eller Azure).

Service Fabric-rapportörer övervakaren identifieras villkor av intresse. De rapport om dessa villkor baserat på deras lokala vyn. Den [hälsoarkivet](service-fabric-health-introduction.md#health-store) samlar in hälsa data skickas av alla rapportörer för att avgöra om enheterna är globalt felfritt. Modellen är avsedd att vara omfattande, flexibla och enkla att använda. Kvaliteten på hälsorapporter anger korrektheten i vyn hälsa i klustret. Falska positiva identifieringar som visar felaktigt ohälsosamt problem kan påverka uppgraderingar och andra tjänster som använder hälsa data. Exempel på sådana tjänster är reparation och mekanismer för aviseringar. Därför behövs tänka för att tillhandahålla rapporter som samlar in villkor för intresse för bästa möjliga sätt.

För att utforma och implementera hälsa reporting watchdogs och systemkomponenter måste:

* Definiera villkoret intressanta och hur den är övervakad inverkan på funktionen kluster eller ett program. Baserat på den här informationen kan besluta om hälsa rapportegenskap och hälsotillstånd.
* Ta reda på [entiteten](service-fabric-health-introduction.md#health-entities-and-hierarchy) som rapporten avser.
* Avgöra var reporting är klart, från tjänsten eller från en intern eller extern watchdog.
* Definiera en datakälla som används för att identifiera personen.
* Välj en reporting strategi regelbundet eller på övergångar. Det rekommenderade sättet är att med jämna mellanrum, eftersom det kräver att enklare koden och är mindre risk för fel.
* Avgör hur lång tid rapporten för ohälsosamt villkor ska vara i health store och hur det ska vara avmarkerade. Med den här informationen kan bestämma time to live-rapportens och ta bort på förfallodatum beteende.

Som tidigare nämnts kan kan reporting göras från:

* Övervakade Service Fabric-tjänsten-repliken.
* Internt watchdogs distribueras som en Service Fabric-tjänsten till exempel ett Service Fabric tillståndslös som övervakar villkor och skickar rapporter. Watchdogs kan vara distribueras ett alla noder eller kan tillhöra den övervakade tjänsten.
* Internt watchdogs som körs på Service Fabric-noder, men som *inte* implementeras som Service Fabric-tjänster.
* Externa watchdogs som avsökning resurs från *utanför* Service Fabric-klustret (till exempel övervakningstjänsten som Gomez).

> [!NOTE]
> Out of box fylls klustret med hälsorapporter som skickas av systemkomponenter. Läs mer på [med systemhälsa rapporter för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Rapporterna som användaren måste skickas [hälsa entiteter](service-fabric-health-introduction.md#health-entities-and-hierarchy) som redan har skapats av systemet.
> 
> 

När hälsotillståndet reporting design är avmarkerad, hälsorapporter kan skickas enkelt. Du kan använda [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) för rapporten hälsa om klustret inte [säker](service-fabric-cluster-security.md) eller om fabric-klienten har administratörsrättigheter. Rapportering kan göras via API: et genom att använda [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), via PowerShell eller via REST. Konfigurationen rattar batch-rapporter för bättre prestanda.

> [!NOTE]
> Rapportera hälsa är synkron och representerar validering arbete på klientsidan. Det faktum att rapporten har godkänts av klientens hälsa eller `Partition` eller `CodePackageActivationContext` objekt innebär inte att tillämpas i arkivet. Skickas asynkront och eventuellt batchar med andra rapporter. Bearbetning på servern misslyckas fortfarande: sekvensnumret kan vara inaktuella, entiteten som rapporten ska tillämpas har tagits bort, osv.
> 
> 

## <a name="health-client"></a>Hälsotillstånd klienten
Hälsorapporter skickas till hälsoarkivet via en hälsa-klient finns i fabric-klienten. Klientens hälsa kan konfigureras med följande inställningar:

* **HealthReportSendInterval**: fördröjning mellan då rapporten läggs till klienten och det skickas till arkivet hälsa. Används för batch-rapporter i ett enda meddelande, i stället för att skicka ett meddelande för varje rapport. Den batchbearbetning förbättrar prestanda. Standard: 30 sekunder.
* **HealthReportRetrySendInterval**: intervallet då hälsa klienten skickar ackumulerade hälsa rapporterar till health store. Standard: 30 sekunder.
* **HealthOperationTimeout**: tidsgränsen för en rapport meddelandet som skickades till health store. Om ett meddelande på grund av timeout, klientens hälsa den tills health store bekräftar att rapporten har bearbetats. Standard: två minuter.

> [!NOTE]
> När rapporterna grupperas fabric-klienten måste hållas aktiva för minst HealthReportSendInterval så att de skickas. Om meddelandet försvinner eller health store kan inte använda dem på grund av tillfälliga fel fabric-klienten måste hållas aktiva längre att ge det en risk att försöka igen.
> 
> 

Buffring på klienten tar unika rapporterna i beräkningen. Till exempel om en viss felaktiga rapport rapporterar 100 rapporter per sekund på samma tillhör samma entitet ersätts rapporterna med den senaste versionen. Högst en sådan rapport finns i kön för klienten. Om batchbearbetning är konfigurerad, är antal rapporter som skickas till arkivet hälsa bara en per skicka intervall. Den här rapporten är sista tillagda rapporten som visar det senaste läget för entiteten.
Ange konfigurationsparametrar när `FabricClient` har skapats genom att skicka [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) med önskade värden hälsa-relaterade poster.

I följande exempel skapar en fabric-klienten och anger att rapporterna ska skickas när de har lagts till. På timeout och fel som kan göras inträffa försök varje 40 sekunder.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Rekommenderar vi att fabric standardklient inställningar som anges för `HealthReportSendInterval` till 30 sekunder. Den här inställningen garanterar optimala prestanda på grund av batchbearbetning. Kritiska rapporter som måste skickas så snart som möjligt, använda `HealthReportSendOptions` med Immediate `true` i [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Omedelbar rapporter kringgå batching intervall. Använd den här flaggan med försiktighet; Vi vill dra nytta av klientens hälsa batchbearbetning när det är möjligt. Omedelbar skicka är också praktisk när du stänger fabric-klienten (t.ex, processen har fastställt ogiltigt tillstånd och måste stängas för att förhindra sidoeffekter). Det garanterar en bästa skicka ackumulerade rapporter. När en rapport läggs med omedelbar flaggan batchar klientens hälsa alla ackumulerade rapporter sedan senaste överföringen.

Samma parametrar kan anges när en anslutning till ett kluster skapas med hjälp av PowerShell. Följande exempel startar en anslutning till det lokala klustret:

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

På liknande sätt API, rapporter kan skickas med `-Immediate` växeln ska skickas omedelbart, oavsett den `HealthReportSendInterval` värde.

För övriga skickas rapporterna till Service Fabric-gatewayen, som har en intern fabric-klienten. Som standard den här klienten är konfigurerad för att skicka rapporter batchar med 30 sekunders mellanrum. Du kan ändra intervallet batch med klustret Konfigurationsinställningen `HttpGatewayHealthReportSendInterval` på `HttpGateway`. Som tidigare nämnts kan ett bättre alternativ är att skicka rapporter med `Immediate` SANT. 

> [!NOTE]
> Konfigurera servern för att godkänna begäranden endast från skyddade klienter för att säkerställa att obehörig services inte kan rapportera hälsa mot entiteter i klustret. Den `FabricClient` används för rapportering måste ha Säkerhetsaktiverade för att kunna kommunicera med klustret (till exempel med Kerberos eller certifikat-autentisering). Läs mer om [kluster säkerhet](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Rapport från i låg behörighet services
Om Service Fabric-tjänster inte har administratörsåtkomst till klustret, kan du rapportera hälsan på enheter från den aktuella kontexten via `Partition` eller `CodePackageActivationContext`.

* För tillståndslösa tjänster använder [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) att rapportera om den aktuella tjänstinstansen.
* Tillståndskänsliga tjänster använder [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) att rapportera om aktuella repliken.
* Använd [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) att rapportera om den aktuella partition entiteten.
* Använd [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) att rapportera om aktuella programmet.
* Använd [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) att rapportera om det aktuella programmet har distribuerats på den aktuella noden.
* Använd [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) att rapportera om inget tjänstepaket för program som distribuerats på den aktuella noden.

> [!NOTE]
> Internt, den `Partition` och `CodePackageActivationContext` håller en hälsa-klient som konfigurerats med standardinställningar. Enligt beskrivningen för den [hälsa klienten](service-fabric-report-health.md#health-client), rapporter batchbearbetas och skickas på en timer. Objekten ska hållas aktiv att ha möjlighet att skicka rapporten.
> 
> 

Du kan ange `HealthReportSendOptions` när du skickar rapporter via `Partition` och `CodePackageActivationContext` hälsa API: er. Om du har kritiska rapporter som måste skickas så snart som möjligt, Använd `HealthReportSendOptions` med Immediate `true`. Omedelbar rapporter kringgå batching intervallet för interna hälsa-klienten. Som tidigare nämnts, Använd den här flaggan med försiktighet; Vi vill dra nytta av klientens hälsa batchbearbetning när det är möjligt.

## <a name="design-health-reporting"></a>Utforma hälsa reporting
Det första steget i skapa hög kvalitet rapporter identifierar de villkor som kan påverka hälsotillståndet för tjänsten. Eventuella villkor som kan hjälpa flaggan problem med tjänsten eller klustret när den startar-- eller ännu bättre innan ett problem inträffar--kan potentiellt spara miljarder dollar. Fördelarna innefattar mindre stillestånd, färre nattetid för att undersöka och reparera problem och högre nöjda.

När villkoren identifieras behöver watchdog skrivare ta reda på det bästa sättet att övervaka dem för balans mellan kostnader och användbarhet. Anta till exempel att en tjänst som utför komplexa beräkningar som använder vissa temporära filer på en nätverksresurs. En watchdog kan övervaka resurs för att säkerställa att det finns tillräckligt med utrymme. Det kan lyssna efter meddelanden om ändringar av filen eller katalogen. Det kan rapportera en varning om ett tröskelvärde för Summa har nåtts och rapportera ett fel om resursen är full. På en varning startade en reparation system Rensa äldre filer på resursen. På ett fel kan ett system för reparation flytta service repliken till en annan nod. Observera hur villkoret tillstånd beskrivs vad gäller hälsa: tillståndet för villkoret som kan uppfattas som Felfri (ok) eller ohälsosamt (varning eller fel).

Efter att övervaka information har angetts måste ta reda på hur du implementerar watchdog watchdog-skrivaren. Om villkoren kan fastställas från inom tjänsten, kan watchdog ingå i den övervakade tjänsten sig själv. Kod som kan exempelvis kontrollera om resursen och rapportera varje gång den försöker att skriva en fil. Fördelen med den här metoden är att reporting är enkel. Vara måste försiktig att förhindra att watchdog buggar påverka funktionen för tjänsten.

Rapportering inifrån är den övervakade tjänsten inte alltid ett alternativ. En watchdog inom tjänsten kanske inte kan identifiera villkoren. Det kan inte ha logik och data att göra en avvägning. Arbetet med att övervaka villkor som kan vara hög. Villkor kan inte heller specifika för en tjänst, men i stället påverkar samverkan mellan tjänster. Ett annat alternativ är att ha watchdogs i klustret som separata processer. Watchdogs övervaka villkor och rapport, utan att påverka tjänster på något sätt. Till exempel kan dessa watchdogs implementeras som tillståndslösa tjänster i samma program som distribuerats på alla noder på samma noder som för tjänsten.

Ibland kan är en watchdog som körs i klustret inte ett alternativ antingen. Om övervakade villkoret är tillgänglighet eller funktion som användarna ser det, är det bäst att watchdogs på samma plats som användaren-klienter. De kan det, testa åtgärder på samma sätt som användare ringa upp dem. Du kan till exempel ha en watchdog som finns utanför klustret, skickar begäranden till tjänsten och kontrollerar den latens och resultatet är korrekt. (För en Kalkylatorn tjänst, till exempel 2 + 2 returnerar 4 inom en rimlig tid?)

När informationen watchdog har avslutats, måste du bestämma på en käll-ID som unikt identifierar den. Om flera watchdogs av samma typ bor i klustret, måste rapporterar på olika enheter, eller om de rapporterar på samma enhet, ska du använda olika käll-ID eller egenskap. Det här sättet sina rapporter kan samexistera. Egenskapen hälsorapporten ska avbilda övervakade villkoret. (Till exempel ovan egenskapen kunde **ShareSize**.) Om flera rapporter gäller samma villkor, bör egenskapen innehålla vissa dynamisk information som tillåter rapporter kan samexistera. Om flera resurser behöver övervakas egenskapsnamnet kan exempelvis **ShareSize sharename**.

> [!NOTE]
> Gör *inte* använda health store för att hålla statusinformation. Hälsorelaterade information ska rapporteras som hälsa, som den här informationen som påverkar hälsotillståndet utvärderingen av en entitet. Health store har inte utformats som ett allmänt Arkiv. Logik för utvärdering av hälsotillstånd används för att aggregera alla data till hälsotillståndet. Skicka information som inte är relaterade till hälsa (som rapporterar status med ett hälsotillstånd OK) påverkar inte aggregerade hälsotillståndet, men det kan påverka prestanda för health store.
> 
> 

Nästa Beslutspunkt är vilken entitet till rapporten på. De flesta fall villkoret tydligt idetifies entiteten. Välj entiteten med bästa möjliga granularitet. Om ett villkor som påverkar alla repliker i en partition, en rapport om partitionen inte på tjänsten. Det finns specialfall där flera tankar krävs dock. Om villkoret påverkar en entitet, till exempel en replik, men att webbprogrammet är att ha villkoret som flaggats för fler än varaktigheten för replik livslängd och redovisas på partitionen. I annat fall när repliken tas bort rensar health store alla rapporter. Watchdog-skrivare måste du tänka livslängd för entiteten och rapporten. Det måste vara tydlig när en rapport måste rensas från en butik (till exempel när ett fel rapporteras på en enhet inte längre gäller).

Nu ska vi titta på ett exempel som sätter ihop punkter I som beskrivs. Överväg ett Service Fabric-program består av en master tillståndskänslig beständig tjänst och sekundära tillståndslösa tjänster som distribueras på alla noder (en sekundär service typ för varje typ av aktivitet). Huvudmålservern har en kö för bearbetning som innehåller kommandon för att köras av sekundärservrar. Sekundära utför inkommande begäranden och skicka tillbaka bekräftelse signaler. Ett villkor som kan övervakas är längden på köns master bearbetning. Om master kölängden når ett tröskelvärde, rapporterade en varning. Varningen anger att sekundära inte kan hantera belastningen. Om kön når maximal längd och kommandon släpps, ett fel rapporteras som tjänsten går inte att återställa. Rapporterna kan vara i egenskapen **QueueStatus**. Watchdog finns i tjänsten och det har skickats på master primära repliken. Time to live är två minuter och skickas regelbundet var 30: e sekund. Om den primära servern kraschar, rensas rapporten automatiskt från store. Om tjänsten repliken är in, men det är ett dödläge eller med andra problem, rapporten upphör att gälla i health store. I det här fallet utvärderas entiteten vid fel.

Ytterligare villkor som kan övervakas är körningstid för aktiviteten. Huvudservern distribuerar uppgifter till sekundärservrar baserat på aktivitetstyp. Beroende på utformning, kunde huvudservern avsöka sekundärservrar för aktivitetsstatus. Det kan också vänta tills sekundärservrar för att skicka tillbaka bekräftelse signaler när de görs. I det andra fallet måste vara försiktig att identifiera situationer där sekundärservrar die eller meddelanden går förlorade. Ett alternativ är att skicka en ping-begäran till samma sekundär som skickar tillbaka dess status. Om ingen status tas emot huvudservern anser att det är ett fel och schemaläggs aktiviteten automatiskt på nytt. Det här beteendet förutsätts att aktiviteterna idempotent.

Övervakade villkoret kan översättas som en varning om aktiviteten inte har gjort i en viss tid (**t1**, till exempel 10 minuter). Om aktiviteten inte slutförs i tid (**t2**, till exempel 20 minuter), övervakade villkoret kan översättas som fel. Den här reporting kan du göra detta på flera olika sätt:

* Den primära repliken master rapporter om sig själv med jämna mellanrum. Du kan ha en egenskap för alla väntande aktiviteter i kön. Om minst en uppgift tar längre tid, Rapportstatus på egenskapen **PendingTasks** är en varning eller fel efter behov. Om det finns inga väntande aktiviteter eller alla aktiviteter startade körningen kan är rapportera status OK. Uppgifter som är permanent. Om den primära servern kraschar, kan den nyligen uppgraderat primärt fortsätta att rapportera korrekt.
* En annan watchdog processen (i molnet eller externa) kontrollerar aktiviteter (från utanför, baserat på resultatet aktiviteten) att se om de är slutförda. Om de inte tar hänsyn till tröskelvärdena, skickas en rapport på tjänsten master. En rapport som också skickas med varje aktivitet som innehåller aktivitets-ID som **PendingTask + taskId**. Rapporter ska skickas endast ohälsosamt tillstånd. Ange tid att live några minuter och markera rapporterna som ska tas bort när de upphör att rensa.
* Den sekundära som kör en uppgift rapporterar när det tar längre tid än väntat att köra den. Rapporterar på tjänstinstansen i egenskapen **PendingTasks**. Rapporten lokaliserar service-instans som har problem, men det fånga inte situation där instansen dör. Rapporterna rensas sedan. Det kan rapportera om tjänsten secondary. Om sekundärt Slutför uppgiften rensar den sekundära instansen rapporten från store. Rapporten fånga inte situation där bekräftelsemeddelandet går förlorad och aktiviteten är inte klar ur den master.

Men reporting görs i de fall som beskrivs ovan, fångas rapporterna i programmets hälsotillstånd när hälsa utvärderas.

## <a name="report-periodically-vs-on-transition"></a>Rapport med jämna mellanrum kontra om övergång
Med hjälp av hälsotillstånd rapportmodellen skicka watchdogs rapporter med jämna mellanrum eller övergångar. Det rekommenderade sättet för rapportering av watchdog är med jämna mellanrum, eftersom koden är mycket enklare och mindre känslig för fel. Watchdogs måste strävar efter att vara så enkelt som möjligt för att undvika buggar som utlöser felaktiga rapporter. Felaktig *ohälsosamt* rapporter påverka hälsa utvärderingar och scenarier baserat på hälsa, inklusive uppgraderingar. Felaktig *felfri* rapporter dölja problem i klustret, som inte används.

För periodiska rapportering, kan watchdog implementeras med en timer. På en timer-återanrop i watchdog Kontrollera tillståndet och skicka en rapport baserad på det aktuella tillståndet. Det finns inget behov av att se vilka som skickats tidigare eller se alla optimeringar vad gäller meddelanden. Klienten hälsotillstånd har batchbearbetning logik för bättre prestanda. När klienten hälsa hålls alive, försöker den internt tills rapporten bekräftas av health store eller watchdog genererar en nyare rapport med samma entitet, egenskap och källa.

Rapportering i övergångar krävs noggrann hantering av tillstånd. Watchdog övervakar vissa villkor och rapporterar endast när villkor som ändrar. Upp-och med den här metoden är att färre rapporter behövs. Nackdelen är att logiken i watchdog komplexa. Watchdog måste upprätthålla villkoren eller rapporter, så att de kan kontrolleras för att avgöra tillstånd ändras. På redundanskluster, måste vara försiktig med rapporter som har lagts till, men ännu inte skickats till health store. Sekvensnumret måste vara allt större. Om inte, avvisas rapporterna som föråldrade. I sällsynta fall där dataförlust uppstår kan synkronisering behövas mellan tillståndet för rapport och tillståndet för health store.

Rapportering av övergångar passar för tjänster som rapporterar på själva via `Partition` eller `CodePackageActivationContext`. När det lokala objektet (replikerings- eller distribuerat tjänstpaket / distribuerat program) är bort alla rapporter tas också bort. Den här automatisk rensning sänker behovet av att synkroniseringen mellan rapport och health store. Om rapporten avser överordnade partitionen eller det överordnade programmet, måste vara försiktig vid redundans att undvika inaktuella rapporter i health store. Logik måste läggas till upprätthålla rätt tillstånd och ta bort rapporten från lagring när det inte behövs längre.

## <a name="implement-health-reporting"></a>Implementera hälsa reporting
När entiteten och rapportera information är klar kan skickar hälsorapporter göras via API: et, PowerShell eller REST.

### <a name="api"></a>API
Om du vill rapportera via API, måste du skapa en hälsorapport specifika för entitetstypen som de vill rapportera om. Ge rapporten till en klient för hälsotillstånd. Du kan också skapa en hälsoinformation och skickar den för att åtgärda rapporteringsmetoder på `Partition` eller `CodePackageActivationContext` att rapportera om aktuella entiteter.

I följande exempel visas periodiska reporting från en watchdog i klustret. Watchdog kontrollerar om en extern resurs kan nås inifrån en nod. Resursen som krävs av en tjänstmanifestet i programmet. Om resursen är tillgänglig, kan andra tjänster i programmet fortfarande att fungera korrekt. Därför skickas rapporten på den distribuerade tjänst paketet entiteten med 30 sekunders mellanrum.

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
Skicka hälsorapporter med **skicka ServiceFabric*EntityType*HealthReport**.

I följande exempel visas periodiska rapportering om CPU-värden på en nod. Rapporterna ska skickas med 30 sekunders mellanrum, och de har en livstiden på två minuter. Om de upphör att gälla har personen problem, så noden som ska utvärderas vid fel. När Processorn överskrider ett tröskelvärde har rapporten ett hälsotillstånd varning. När Processorn överskrider ett tröskelvärde för mer än den konfigurerade tidpunkten, rapporteras den som ett fel. Annars skickar personen ett hälsotillstånd OK.

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

I följande exempel rapporterar en varning om tillfälligt på en replik. Först hämtar partitions-ID och replik-ID för tjänsten som den är intresserad av. Skickar sedan en rapport från **PowershellWatcher** i egenskapen **ResourceDependency**. Rapporten är av intresse för bara två minuter och det bort automatiskt från arkivet.

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
Skicka hälsorapporter med POST-förfrågningar som går du till entitet och har i själva hälsa Rapportbeskrivning REST. Till exempel visas hur du skickar REST [kluster hälsorapporter](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) eller [tjänsten hälsorapporter](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Alla enheter som stöds.

## <a name="next-steps"></a>Nästa steg
Baserat på data i hälsa, kan service-skrivare och kluster/application-administratörer Se sätt att använda informationen. De kan till exempel konfigurera aviseringar baserat på hälsostatus för att fånga allvarliga problem innan de leda till avbrott. Administratörer kan också ställa in reparera system för att åtgärda problem automatiskt.

[Introduktion till Service Fabric hälsa övervakning](service-fabric-health-introduction.md)

[Visa Service Fabric-hälsorapporter](service-fabric-view-entities-aggregated-health.md)

[Hur du rapporten och kontrollera tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Använda systemet hälsorapporter för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)

