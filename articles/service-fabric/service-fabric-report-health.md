---
title: Lägg till anpassade hälsorapporter i Service Fabric | Microsoft Docs
description: Beskriver hur du skickar anpassade hälsorapporter i Azure Service Fabric health entiteter. Innehåller rekommendationer för att utforma och implementera kvalitet hälsorapporter.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 49ebf4ab95816a3da2f74a464b12b46de6228456
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280561"
---
# <a name="add-custom-service-fabric-health-reports"></a>Lägga till anpassade hälsorapporter i Service Fabric
Azure Service Fabric introducerar en [hälsomodellen](service-fabric-health-introduction.md) utformats för att flaggan feltillstånd kluster och program villkor för specifika entiteter. Hälsomodell använder **hälsotillstånd rapportörer** (systemkomponenter och watchdogs). Målet är enkel och snabb diagnos och reparation. Skrivare för tjänsten måste du tänka igenom initiala om hälsotillstånd. Alla villkor som kan påverka hälsotillståndet redovisas, särskilt om det kan hjälpa att flaggan problem nära roten. Hälsoinformation kan spara tid och möda på felsökning och undersökning. Led är särskilt tydligt när tjänsten är igång och körs i skala i molnet (privat eller Azure).

Service Fabric-rapportörer övervakaren identifierat villkor av intresse. De rapport om dessa villkor baserat på deras lokala vyn. Den [hälsoarkivet](service-fabric-health-introduction.md#health-store) aggregerar hälsodata som skickas av alla rapportörer att avgöra om entiteter är globalt felfritt. Modellen är avsedd att vara omfattande, flexibelt och enkelt att använda. Kvaliteten på hälsorapporterna anger precisionen i vyn hälsotillstånd för klustret. Falska positiva identifieringar som felaktigt visar felaktiga problem kan negativt påverka uppgraderingar eller andra tjänster som använder hälsodata. Exempel på sådana tjänster är reparation och mekanismer för aviseringar. Därför behövs upp för att tillhandahålla rapporter som samlar in villkor av intresse för bästa möjliga sätt.

För att utforma och implementera tillståndsrapportering, watchdogs och systemkomponenter måste:

* Definiera villkoret intressanta och hur den är övervakad effekten på funktionen kluster eller ett program. Baserat på den här informationen kan besluta om hälsotillstånd rapportegenskap och hälsotillstånd.
* Fastställa den [entitet](service-fabric-health-introduction.md#health-entities-and-hierarchy) rapporten avser.
* Avgöra var reporting är klar, från tjänsten eller från en intern eller extern watchdog.
* Definiera en källa som används för att identifiera personen.
* Välj en reporting strategi med jämna mellanrum eller på övergångar. Det rekommenderade sättet är att med jämna mellanrum, eftersom den kräver enklare kod och är mindre känslig för fel.
* Avgör hur lång tid rapporten för ohälsosamt villkor ska vara i health store och hur det ska vara avmarkerat. Med den här informationen kan bestämma Rapporttid TTL-värde och ta bort på förfallodatum beteende.

Som tidigare nämnts kan reporting göras från:

* Övervakade Service Fabric-tjänst-repliken.
* Interna watchdogs distribueras som en Service Fabric-tjänst (till exempel en tillståndslös Service Fabric tjänst som övervakar villkor och skickar rapporter). Watchdogs kan vara distribueras en alla noder eller kan tillhöra den övervakade tjänsten.
* Interna watchdogs som körs på Service Fabric-noder, men är *inte* implementeras som Service Fabric-tjänster.
* Externa watchdogs som avsökning resursen från *utanför* Service Fabric-klustret (till exempel övervakningstjänst som Gomez).

> [!NOTE]
> Direkt fylls klustret med hälsorapporter som skickas av systemkomponenter. Läs mer i [med systemhälsa rapporter för att felsöka](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Användarrapporter måste skickas [hälsotillstånd entiteter](service-fabric-health-introduction.md#health-entities-and-hierarchy) som redan har skapats av systemet.
> 
> 

När hälsotillståndet reporting design är tydligt, hälsorapporter kan skickas enkelt. Du kan använda [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) för rapporten hälsa om klustret inte [säker](service-fabric-cluster-security.md) eller om klienten fabric har administratörsrättigheter. Rapportering kan göras via API: et genom att använda [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), via PowerShell eller via REST. Konfigurationen rattar batch-rapporter för bättre prestanda.

> [!NOTE]
> Rapportera hälsa är synkron, och rollen representerar verifiering arbete på klientsidan. Det faktum att rapporten har godkänts av hälsotillstånd klienten eller `Partition` eller `CodePackageActivationContext` objekt innebär inte att tillämpas i arkivet. Det är skickats asynkront och eventuellt batchar med andra rapporter. Bearbetning på servern kan fortfarande misslyckas: sekvensnumret kan vara inaktuella, där rapporten måste tillämpas entiteten har tagits bort, osv.
> 
> 

## <a name="health-client"></a>Hälsotillstånd klienten
Rapporter om hälsotillstånd skickas till hälsoindikatorn via en health-klienten, som finns i fabric-klienten. Hälsoindikatorn sparar rapporter i health store. Health-klienten kan konfigureras med följande inställningar:

* **HealthReportSendInterval**: Fördröjningen mellan den tid som rapporten läggs till klienten och tid det skickas till hälsoindikatorn. Används för att batch-rapporter till ett enda meddelande, i stället för att skicka ett meddelande för varje rapport. Den batchbearbetning förbättrar prestandan. Standard: 30 sekunder.
* **HealthReportRetrySendInterval**: Intervallet då hälsotillstånd klienten skickar ackumulerade hälsotillstånd rapporterar till hälsoindikatorn. Standard: 30 sekunders mellanrum, minsta: 1 sekund.
* **HealthOperationTimeout**: Tidsgränsen för en rapport-meddelanden som skickas till hälsoindikatorn. Om ett meddelande tidsgränsen hälsotillstånd klienten ett nytt försök den tills hälsoindikatorn bekräftar att rapporten har bearbetats. Standard: två minuter.

> [!NOTE]
> När rapporterna grupperas fabric klienten måste hållas alive för minst HealthReportSendInterval så att de skickas. Om meddelandet går förlorad eller hälsoindikatorn kan inte använda dem på grund av tillfälliga fel, fabric klienten måste hållas aktiv längre att ge det en risk att försöka igen.
> 
> 

Buffring på klienten tar unikhet av rapporterna i beräkningen. Om en viss felaktig rapport rapporterar 100 rapporter per sekund på samma egenskap för samma entitet, till exempel ersätts rapporterna med den senaste versionen. Högst en sådan rapport finns i kön för klienten. Om batchbearbetning har konfigurerats är antalet rapporter som skickas till hälsoindikatorn bara en per skicka intervall. Den här rapporten är den senaste har lagts tillagda rapporten, som visar senaste tillståndet för entiteten.
Ange konfigurationsparametrar när `FabricClient` har skapats genom att skicka [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) med önskade värden health-relaterade poster.

I följande exempel skapar en fabric-klient och anger att rapporterna ska skickas när de har lagts till. Återförsök sker på tidsgränser och fel som kan försökas sekunders 40.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Vi rekommenderar att fabric standardklient inställningarna som angetts `HealthReportSendInterval` 30 sekunder. Den här inställningen säkerställer optimala prestanda på grund av batchbearbetning. Kritiska rapporterna som måste skickas så snart som möjligt, använda `HealthReportSendOptions` med Immediate `true` i [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Omedelbar rapporter kringgå batchbearbetningen intervallet. Använd den här flaggan med försiktighet; Vi vill dra nytta av hälsotillstånd klienten batchbearbetning när det är möjligt. Omedelbar skicka är också användbart när klienten fabric stängs (till exempel processen har fastställt ogiltigt tillstånd och måste stängas för att förhindra sidoeffekter). Det innebär att skicka mån av ackumulerade rapporterna. När du lägger till en rapport med omedelbar flaggan, health klienten att slår ihop alla ackumulerade rapporter sedan senaste sändning.

Samma parametrar kan anges när en anslutning till ett kluster har skapats via PowerShell. Följande exempel startar en anslutning till ett lokalt kluster:

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

På samma sätt till API: et, rapporterna kan skickas med hjälp av `-Immediate` växel ska skickas omedelbart, oavsett den `HealthReportSendInterval` värde.

För REST skickas rapporterna till Service Fabric-gateway som har en intern fabric-klient. Som standard den här klienten är konfigurerad för att skicka rapporter batchar med 30 sekunders mellanrum. Du kan ändra intervallet för batch med klustret Konfigurationsinställningen `HttpGatewayHealthReportSendInterval` på `HttpGateway`. Som tidigare nämnts, ett bättre alternativ är att skicka rapporter med `Immediate` SANT. 

> [!NOTE]
> Konfigurera servern för att godkänna begäranden endast från skyddade klienter för att säkerställa att obehörig tjänster inte kan rapportera hälsa mot entiteter i klustret. Den `FabricClient` används för rapportering måste ha security aktiverat för att kunna kommunicera med klustret (till exempel med Kerberos eller certifikatautentisering). Läs mer om [kluster security](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Rapporten från i services med låg behörighet
Om Service Fabric-tjänster inte har administratörsåtkomst till klustret, kan du rapportera hälsotillstånd på entiteter från den aktuella kontexten via `Partition` eller `CodePackageActivationContext`.

* För tillståndslösa tjänster kan använda [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) att rapportera om den aktuella tjänstinstansen.
* Tillståndskänsliga tjänster, använda [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) att rapportera om aktuella repliken.
* Använd [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) att rapportera om den aktuella partition-entiteten.
* Använd [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) att rapportera om aktuella program.
* Använd [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) att rapportera om det aktuella programmet som distribuerats på den aktuella noden.
* Använd [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) att rapportera om inget tjänstepaket för program som distribuerats på den aktuella noden.

> [!NOTE]
> Internt i `Partition` och `CodePackageActivationContext` håller en health-klient som konfigurerats med standardinställningar. Enligt beskrivningen för den [hälsotillstånd klienten](service-fabric-report-health.md#health-client), rapporter batchar och skickas av en timer. Objekten ska hållas aktiv ska få möjligheten att skicka rapporten.
> 
> 

Du kan ange `HealthReportSendOptions` när du skickar rapporter via `Partition` och `CodePackageActivationContext` hälsotillstånd API: er. Om du har kritiska rapporterna som måste skickas så snart som möjligt, Använd `HealthReportSendOptions` med Immediate `true`. Omedelbar rapporter kringgå batchbearbetningen intervallet av interna health-klienten. Som tidigare nämnts, Använd den här flaggan med försiktighet; Vi vill dra nytta av hälsotillstånd klienten batchbearbetning när det är möjligt.

## <a name="design-health-reporting"></a>Utforma tillståndsrapportering
Det första steget i genereringen av hög kvalitet rapporter identifierar de villkor som kan påverka hälsotillståndet för tjänsten. Alla villkor som kan hjälpa att flaggan problem i tjänsten eller klustret när den startar-- eller ännu bättre innan det uppstår--kan potentiellt spara miljarder dollar. Fördelarna omfattar mindre driftstopp, färre nattetid för att undersöka och reparera problem och högre kundnöjdhet.

När villkoren identifieras måste watchdog-skrivare att ta reda på det bästa sättet att övervaka dem för balans mellan omkostnader och användbarhet. Anta exempelvis att en tjänst som utför komplexa beräkningar som använder vissa temporära filer på en resurs. En watchdog kan övervaka resursen för att säkerställa att det finns tillräckligt med utrymme. Det kan lyssna efter meddelanden om ändringar av filen eller katalogen. Det kan rapportera en varning om ett tröskelvärde som betalats i förskott har nåtts och rapportera ett fel om resursen är full. En varning, kan ett system för reparation starta rensar äldre filer på resursen. En repair-system kan flytta tjänsterepliken till en annan nod på ett fel. Observera hur villkor tillstånd beskrivs vad gäller hälsotillstånd: tillståndet för de villkor som kan ses felfritt (ok) eller defekt (varning eller fel).

När övervakning information har angetts måste en watchdog-skrivare ta reda på hur du implementerar watchdog. Om villkoren kan fastställas från i tjänsten, kan watchdog vara en del av den övervakade tjänsten själva. Kod som kan exempelvis kontrollera om filresursen och rapportera sedan varje gång den försöker skriva en fil. Fördelen med den här metoden är att reporting är enkel. Försiktighet måste vidtas för att förhindra att watchdog buggar kan påverka funktionen för tjänsten.

Rapportering inifrån är den övervakade tjänsten inte alltid ett alternativ. En watchdog i tjänsten kanske inte kan identifiera villkoren. Den kan inte ha logik eller data för att göra en avvägning. Arbetet med att övervaka villkor som kan vara hög. Villkoren kan inte heller att specifika för en tjänst, men i stället påverka interaktioner mellan tjänster. Ett annat alternativ är att ha watchdogs i klustret som separata processer. Watchdogs övervaka villkor och rapport, utan att påverka tjänster på något sätt. Till exempel kan dessa watchdogs implementeras som tillståndslösa tjänster i samma program distribueras på alla noder eller på samma noderna som tjänsten.

Ibland kan är en watchdog som körs i klustret inte ett alternativ för antingen. Om övervakade villkoret är tillgänglighet eller i tjänsten som användarna ser det, är det bäst att ha watchdogs på samma plats som användaren-klienter. Där kan de testa åtgärderna på samma sätt som användare anropa dem. Du kan till exempel ha en watchdog som finns utanför klustret, skickar begäranden till tjänsten och kontrollerar svarstider och är korrekt på resultatet. (För en kalkylator-tjänst, till exempel 2 + 2 returnerar 4 inom en rimlig tid?)

När watchdog-information har avslutats, bör du bestämma på en käll-ID som unikt identifierar den. Om flera watchdogs av samma typ leva i klustret, måste rapporterar på olika enheter eller, om de rapporterar på samma entitet, använda olika käll-ID eller egenskapen. På så sätt kan sina rapporter kan existera tillsammans. Egenskapen för hälsorapporten bör läsa in övervakade villkoret. (För exemplet ovan är egenskapen kan vara **ShareSize**.) Om flera rapporter gäller för samma villkor, ska egenskapen innehålla vissa dynamisk information som gör att rapporter ska kunna användas samtidigt. Exempel: Om flera resurser behöver övervakas egenskapsnamnet kan vara **ShareSize sharename**.

> [!NOTE]
> Gör *inte* använda health store för att hålla statusinformation. Endast health-relaterad information ska rapporteras som hälsa, som den här informationen påverkar hälsotillståndet utvärderingen av en entitet. Health store har inte utformats som ett allmänt Arkiv. Hälsotillstånd utvärderingslogiken används för att samla ihop alla data i hälsotillståndet. Skicka information som inte är relaterade till hälsotillstånd (till exempel rapporterar status med ett hälsotillstånd OK) påverkar inte aggregerade hälsotillståndet, men det kan påverka prestanda för health store.
> 
> 

Nästa beslutspunkten är vilken entitet till rapporten på. I de flesta fall, identifierar villkoret tydligt entiteten. Välj entiteten med bästa möjliga granularitet. Om ett villkor påverkar alla repliker i en partition får en rapport om partitionen inte på tjänsten. Det finns hörnet fall där flera ansedda krävs dock. Om villkoret påverkar en enhet, till exempel en replik, men önskan är att ha villkoren som har flaggats för mer än repliken livstid och redovisas på partitionen. I annat fall när repliken tas bort rensar i hälsoarkivet alla rapporter. Watchdog-skrivare måste tänka livslängd för entiteten och rapporten. Det måste vara tydligt när en rapport ska rensas från en butik (till exempel när ett fel rapporteras på en enhet inte längre gäller).

Nu ska vi titta på ett exempel som sätter ihop punkter I som beskrivs. Överväg att ett Service Fabric-program som består av en master tillståndskänsliga permanenta tjänster och sekundära tillståndslösa tjänster som distribueras på alla noder (en tjänsten secondary typ för varje typ av aktivitet). Huvuddatabasen har en kö för bearbetning som innehåller kommandon för att köras av sekundärservrar. Sekundära kör inkommande begäranden och skicka tillbaka bekräftelse signaler. Ett villkor som kan övervakas är längden på bearbetningskön master. Om master kölängden når ett tröskelvärde kan rapporteras en varning. Varningen anger att de sekundära databaser inte kan hantera belastningen. Om kön når den maximala längden och kommandon ignoreras, ett fel rapporteras som tjänsten inte kan återställa. Rapporterna kan finnas på egenskapen **QueueStatus**. Watchdog finns i tjänsten och den skickas regelbundet på den överordnade primära repliken. Time to live är två minuter och skickas regelbundet med 30 sekunders mellanrum. Om den primära servern slutar fungera kan rensas rapporten automatiskt från store. Om tjänsterepliken är igång, men det är ett dödläge eller har andra problem, rapporten går ut i health store. I det här fallet utvärderas entiteten vid fel.

Ytterligare ett villkor som kan övervakas är körningstid för uppgiften. Huvudservern distribueras uppgifter till de sekundära databaser beroende på vilken uppgift. Beroende på utformning, kunde huvudservern avsöka sekundärservrar för aktivitetens status. Det kan också vänta tills sekundärservrar för att skicka tillbaka bekräftelse signaler när de är klar. I det andra fallet måste du vara noggrann att identifiera situationer där sekundärservrar chips eller meddelanden går förlorade. Ett alternativ är att skicka en ping-begäran till samma sekundärt, som skickar tillbaka statusen. Om ingen status tas emot, så att den fel huvudservern och schemalägger om uppgiften. Detta förutsätter att uppgifterna är idempotenta.

Övervakade villkoret kan översättas som en varning om aktiviteten inte har gjort i en viss tid (**t1**, till exempel 10 minuter). Om aktiviteten inte slutförs tidpunkt (**t2**, till exempel 20 minuter), övervakade villkoret kan översättas som fel. Den här reporting kan du göra detta på flera olika sätt:

* Den överordnade primära repliken rapporterar om själva regelbundet. Du kan ha en egenskap för alla väntande aktiviteter i kön. Om minst en uppgift tar längre tid, Rapportstatus på egenskapen **PendingTasks** är en varning eller fel, efter behov. Om det finns inga väntande uppgifter eller alla uppgifter startade körningen, är rapportera status OK. Aktiviteterna är permanent. Om den primära servern slutar fungera kan kan den nyligen uppgraderade primärt fortsätta att rapportera korrekt.
* En annan watchdog-processen (i molnet eller externa) kontrollerar uppgifterna (från utanför, baserat på den önskade Uppgiftsresultat) att se om de har slutförts. Om de inte respekterar tröskelvärdena, skickas en rapport på huvudtjänsten. En rapport skickas också på varje aktivitet som innehåller aktiviteten-identifierare som **PendingTask + taskId**. Rapporter ska skickas endast om ohälsosamt tillstånd. Ange tiden för live till några minuter och markera rapporter som ska tas bort när de går ut för att se till att rensa.
* Den sekundära som kör en uppgift rapporterar när det tar längre tid än förväntat att köra den. Den rapporterar på tjänstinstans på egenskapen **PendingTasks**. Rapporten lokaliserar den tjänstinstans som har problem, men den inte situationen där instansen dör. Rapporterna rensas sedan. Det kan rapportera om tjänsten secondary. Om sekundärt har slutförts uppgiften rensar den sekundära instansen rapporten från store. Rapporten fånga inte situationen där bekräftelsemeddelandet går förlorad och uppgiften är inte klar från den master synsätt.

Men reporting görs i de fall som beskrivs ovan, fångas rapporterna i programmets hälsotillstånd när hälsotillstånd utvärderas.

## <a name="report-periodically-vs-on-transition"></a>Rapport med jämna mellanrum jämfört med om övergång
Genom att använda reporting hälsomodellen skicka watchdogs rapporter med jämna mellanrum eller på övergångar. Det rekommenderade sättet för watchdog rapportering är med jämna mellanrum, eftersom koden är mycket enklare och mindre känslig för fel. Watchdogs måste strävar efter att vara så enkla som möjligt för att undvika buggar som utlöser felaktiga rapporter. Felaktig *feltillstånd* rapporter påverka hälsotillståndet utvärderingar och scenarier som baseras på hälsotillstånd, inklusive uppgraderingar. Felaktig *felfri* rapporter dölja problem i klustret, som inte är önskvärt.

Watchdog kan implementeras med en timer för periodiska rapportering. På en timer-motringning watchdog Kontrollera tillståndet och skicka en rapport som baseras på det aktuella tillståndet. Det finns inget behov att se vilken rapport har skickats tidigare eller göra några optimeringar när det gäller meddelanden. Klienten hälsotillstånd har batchbearbetning logik för att hjälpa till med prestanda. När klienten hälsotillstånd förblir aktiv, ett nytt försök görs internt tills rapporten bekräftas av health store eller watchdog genererar en nyare rapport med samma entitet och egenskap och källa.

Rapportering om övergångar kräver noggrann hantering av tillstånd. Watchdog övervakar vissa villkor och rapporterar endast när villkoren ändras. Upp-och med den här metoden är att färre rapporter krävs. Nackdelen är att logiken watchdog är komplexa. Watchdog måste ha villkoren eller rapporter, så att de kan granskas för att fastställa tillståndsändringar. Vid redundans, måste du vara noggrann med rapporter har lagts till, men har ännu inte skickas till health store. Sekvensnumret måste vara begripliga. Om inte, avvisas rapporterna som inaktuell. I sällsynta fall där dataförlust uppkommer kan synkronisering behövas mellan personen tillstånd och status för health store.

Rapportering om övergångar passar för tjänster som rapporterar på själva via `Partition` eller `CodePackageActivationContext`. När det lokala objektet (replik eller distribuerat tjänstpaket / distribuerat program) har tagits bort kan alla rapporter tas också bort. Den här automatisk rensning sänker behovet av synkronisering mellan rapport och hälsoarkivet. Om rapporten avser överordnade partitionen eller det överordnade programmet, måste du vara noggrann vid redundans att undvika inaktuella rapporter i health store. Logik måste läggas till har rätt tillstånd och avmarkera rapporten från store när det inte behövs längre.

## <a name="implement-health-reporting"></a>Implementera tillståndsrapportering
När information om entiteten och rapporten är tydliga, kan skicka rapporter om hälsotillstånd göras via API, PowerShell eller REST.

### <a name="api"></a>API
Om du vill rapportera via API måste du skapa en hälsorapport som är specifika för den enhetstyp som de vill rapportera om. Ge rapporten till en klient för hälsotillstånd. Du kan också skapa en hälsoinformation och skickar den för att åtgärda rapporteringsmetoder på `Partition` eller `CodePackageActivationContext` att rapportera om aktuella entiteter.

I följande exempel visas periodiska rapportering från en watchdog i klustret. Watchdog kontrollerar om en extern resurs kan nås inifrån en nod. Resursen krävs av ett tjänstmanifest i programmet. Om resursen är tillgänglig, kan de andra tjänsterna i programmet fortfarande att fungera korrekt. Därför skickas rapporten i den distribuerade tjänst paketet entiteten med 30 sekunders mellanrum.

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
Skicka rapporter om hälsotillstånd med **skicka ServiceFabric*EntityType*HealthReport**.

I följande exempel visas periodiska rapportering om CPU-värden på en nod. Rapporterna ska skickas med 30 sekunders mellanrum, och de har en tid TTL-värde på två minuter. Om de går ut har personen problem, så att noden ska utvärderas vid fel. När Processorn är överskrider ett visst tröskelvärde, har rapporten ett hälsotillstånd varning. När Processorn är längre än den konfigurerade tiden kvar överskrider ett visst tröskelvärde, rapporteras den som ett fel. I annat fall skickar personen ett hälsotillstånd OK.

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

I följande exempel rapporterar en tillfälliga varning om en replik. Först hämtar partitions-ID och sedan på replik-ID för tjänsten som den är intresserad av. Därefter skickas en rapport från **PowershellWatcher** på egenskapen **ResourceDependency**. Rapporten är av intresse i två minuter och den bort automatiskt från arkivet.

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
Skicka rapporter om hälsotillstånd med hjälp av REST med POST-förfrågningar som går du till önskad entitet och har i själva Rapportbeskrivning hälsotillstånd. Till exempel visas hur du skickar REST [kluster hälsorapporter](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) eller [tjänsten hälsorapporter](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Alla entiteter stöds.

## <a name="next-steps"></a>Nästa steg
Baserat på de health kan service skrivare och -kluster/programadministratörer tänka på olika sätt att använda informationen. De kan till exempel skapa aviseringar baserat på hälsostatus för att fånga upp allvarliga problem innan de leda till avbrott. Administratörer kan också ställa in reparera system för att åtgärda problem automatiskt.

[Introduktion till Service Fabrics hälsa övervakning](service-fabric-health-introduction.md)

[Visa hälsorapporter i Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Hjälp för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uppgradera Service Fabric-programmet](service-fabric-application-upgrade.md)

