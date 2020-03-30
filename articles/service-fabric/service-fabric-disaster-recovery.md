---
title: Azure Service Fabric haveriberedskap
description: Azure Service Fabric erbjuder funktioner för att hantera katastrofer. I den här artikeln beskrivs vilka typer av katastrofer som kan inträffa och hur du hanterar dem.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b29985d40ae3a1bf582099e998e000fed83460f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371655"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Haveriberedskap i Azure Service Fabric
En viktig del av att leverera hög tillgänglighet är att se till att tjänster kan överleva alla olika typer av fel. Detta är särskilt viktigt för fel som är oplanerade och utanför din kontroll. 

I den här artikeln beskrivs några vanliga fellägen som kan vara katastrofer om de inte modelleras och hanteras korrekt. Den diskuterar också mildrande åtgärder och åtgärder att vidta om en katastrof inträffar ändå. Målet är att begränsa eller eliminera risken för driftstopp eller dataförlust när fel, planerade eller på annat sätt, inträffar.

## <a name="avoiding-disaster"></a>Undvika katastrof
Huvudsyftet med Azure Service Fabric är att hjälpa dig att modellera både din miljö och dina tjänster på ett sådant sätt att vanliga feltyper inte är katastrofer. 

I allmänhet finns det två typer av katastrof-/felscenarier:
- Maskinvaru- och programvarufel
- Driftfel

### <a name="hardware-and-software-faults"></a>Maskinvaru- och programvarufel
Maskinvaru- och programvarufel är oförutsägbara. Det enklaste sättet att överleva fel är att köra fler kopior av tjänsten över hårdvara eller programvara fel gränser. 

Om tjänsten till exempel körs på endast en dator är felet för den datorn en katastrof för den tjänsten. Det enkla sättet att undvika denna katastrof är att se till att tjänsten körs på flera datorer. Testning är också nödvändigt för att säkerställa att fel på en maskin inte stör den löpande tjänsten. Kapacitetsplanering säkerställer att en ersättningsinstans kan skapas någon annanstans och att kapacitetsminskningen inte överbelastar de återstående tjänsterna. 

Samma mönster fungerar oavsett vad du försöker undvika misslyckandet med. Om du till exempel är orolig för felet i ett SAN körs du över flera SAN-nätverk. Om du är orolig för förlusten av ett rack av servrar, du stöter på flera rack. Om du är orolig för förlusten av datacenter bör din tjänst köras över flera Azure-regioner, över flera Azure-tillgänglighetszoner eller i dina egna datacenter. 

När en tjänst sträcker sig över flera fysiska instanser (datorer, rack, datacenter, regioner) är du fortfarande föremål för vissa typer av samtidiga fel. Men enstaka och till och med flera fel av en viss typ (till exempel en enda virtuell dator eller nätverkslänk misslyckas) hanteras automatiskt och är därför inte längre en "katastrof". 

Service Fabric tillhandahåller mekanismer för att expandera klustret och hanterar föra misslyckade noder och tjänster tillbaka. Service Fabric gör det också möjligt att köra många instanser av dina tjänster för att förhindra oplanerade fel från att förvandlas till verkliga katastrofer.

Det kan finnas orsaker till att det inte är möjligt att köra en distribution som är tillräckligt stor för att kunna ta sig över fel. Det kan till exempel krävas fler maskinvaruresurser än du är villig att betala för i förhållande till risken för fel. När du hanterar distribuerade program kan ytterligare kommunikationshopp eller tillståndsreplikeringskostnader över geografiska avstånd orsaka oacceptabla svarstid. Om denna linje dras skiljer sig åt för varje program. 

För programvarufel specifikt kan felet bero på den tjänst som du försöker skala. I det här fallet förhindrar fler kopior inte katastrofen, eftersom felvillkoret är korrelerat mellan alla instanser.

### <a name="operational-faults"></a>Driftfel
Även om din tjänst sträcker sig över hela världen med många uppsägningar, kan det fortfarande uppleva katastrofala händelser. Någon kan till exempel av misstag konfigurera om DNS-namnet för tjänsten eller ta bort det direkt. 

Anta att du har en tillståndskänslig Service Fabric-tjänst och någon har tagit bort tjänsten av misstag. Om det inte finns någon annan lindring, den tjänsten och hela staten att den hade är nu borta. Dessa typer av operativa katastrofer ("oops") kräver olika mildrande åtgärder och steg för återställning än vanliga oplanerade fel. 

De bästa sätten att undvika dessa typer av driftfel är att:
- Begränsa driftåtkomsten till miljön.
- Strikt granska farliga operationer.
- Införa automatisering, förhindra manuella eller out-of-band-ändringar och validera specifika ändringar mot miljön innan du antar dem.
- Se till att destruktiva operationer är "mjuka". Mjuka åtgärder börjar inte gälla omedelbart eller kan ångras inom ett tidsfönster.

Service Fabric tillhandahåller mekanismer för att förhindra driftfel, till exempel tillhandahålla [rollbaserad](service-fabric-cluster-security-roles.md) åtkomstkontroll för klusteråtgärder. De flesta av dessa driftfel kräver dock organisatoriska insatser och andra system. Service Fabric tillhandahåller mekanismer för att överleva driftfel, framför allt [säkerhetskopiering och återställning för tillståndskänsliga tjänster](service-fabric-backuprestoreservice-quickstart-azurecluster.md).

## <a name="managing-failures"></a>Hantera fel
Målet med Service Fabric är automatisk hantering av fel. Men för att hantera vissa typer av fel måste tjänsterna ha ytterligare kod. Andra typer av fel bör _inte_ åtgärdas automatiskt av säkerhets- och kontinuitetsskäl. 

### <a name="handling-single-failures"></a>Hantera enstaka fel
Enstaka maskiner kan misslyckas av alla möjliga skäl. Ibland är det hårdvara orsaker, som nätaggregat och fel nätverksmaskinvara. Andra fel finns i programvara. Dessa inkluderar fel i operativsystemet och själva tjänsten. Service Fabric identifierar automatiskt dessa typer av fel, inklusive fall där datorn isoleras från andra datorer på grund av nätverksproblem.

Oavsett typ av tjänst, kör en enda instans resulterar i driftstopp för den tjänsten om den enda kopian av koden misslyckas av någon anledning. 

För att hantera ett enskilt fel är det enklaste du kan göra att se till att dina tjänster körs på mer än en nod som standard. För tillståndslösa tjänster, `InstanceCount` se till att det är större än 1. För tillståndskänsliga tjänster är `TargetReplicaSetSize` `MinReplicaSetSize` minimirekommendationen den och är båda inställda på 3. Om du kör fler kopior av din servicekod säkerställer du att tjänsten kan hantera ett enskilt fel automatiskt. 

### <a name="handling-coordinated-failures"></a>Hantera samordnade fel
Samordnade fel i ett kluster kan bero på antingen planerade eller oplanerade infrastrukturfel och ändringar eller planerade programvaruändringar. Service Fabric modeller infrastrukturzoner som upplever samordnade fel som *fel domäner*. Områden som kommer att uppleva samordnade programändringar modelleras som *uppgraderingsdomäner*. Mer information om feldomäner, uppgraderingsdomäner och klustertopologi finns i [Beskriv ett service fabric-kluster med hjälp av Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md).

Som standard tar Service Fabric hänsyn till fel- och uppgraderingsdomäner när du planerar var dina tjänster ska köras. Som standard försöker Service Fabric se till att dina tjänster körs över flera fel- och uppgraderingsdomäner så att dina tjänster förblir tillgängliga om planerade eller oplanerade ändringar sker. 

Anta till exempel att fel på en strömkälla gör att alla datorer på ett rack misslyckas samtidigt. Med flera kopior av tjänsten igång, förlust av många datorer i fel domän fel förvandlas till bara ett annat exempel på ett enda fel för en tjänst. Det är därför hantera fel och uppgradera domäner är avgörande för att säkerställa hög tillgänglighet för dina tjänster. 

När du kör Service Fabric i Azure hanteras feldomäner och uppgraderingsdomäner automatiskt. I andra miljöer kanske de inte är det. Om du bygger egna kluster lokalt måste du mappa och planera feldomänlayouten på rätt sätt.

Uppgraderingsdomäner är användbara för modelleringsområden där programvara kommer att uppgraderas samtidigt. På grund av detta definierar uppgraderingsdomäner också ofta gränserna där programvara tas bort under planerade uppgraderingar. Uppgraderingar av både Service Fabric och dina tjänster följer samma modell. Mer information om rullande uppgraderingar, uppgraderingsdomäner och hälsomodellen Service Fabric som hjälper till att förhindra att oavsiktliga ändringar påverkar klustret och tjänsten finns i:

 - [Uppgradering av programmet](service-fabric-application-upgrade.md)
 - [Självstudiekurs för uppgradering av program](service-fabric-application-upgrade-tutorial.md)
 - [Hälsomodell för service fabric](service-fabric-health-introduction.md)

Du kan visualisera klustrets layout med hjälp av klustermappningen i [Service Fabric Explorer:](service-fabric-visualizing-your-cluster.md)

<center>

![Noder spridda över feldomäner i Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modellering områden av fel, rullande uppgraderingar, kör många instanser av din tjänstkod och tillstånd, placeringsregler för att säkerställa att dina tjänster körs över fel och uppgradera domäner, och inbyggd hälsoövervakning är bara *några* av de funktioner som Service Fabric ger för att hålla normala operativa problem och fel från att förvandlas till katastrofer. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Hantera samtidiga maskinvaru- eller programvarufel
Vi har pratat om singelfel. Som du kan se är de lätta att hantera för både tillståndslösa och tillståndskänsliga tjänster bara genom att hålla fler kopior av koden (och tillståndet) som körs över fel- och uppgraderingsdomäner. 

Flera samtidiga slumpmässiga fel kan också inträffa. Dessa är mer benägna att leda till driftstopp eller en verklig katastrof.


#### <a name="stateless-services"></a>Statslösa tjänster
Instansantalet för en tillståndslös tjänst anger önskat antal instanser som måste köras. När någon (eller alla) av instanserna misslyckas, svarar Service Fabric genom att automatiskt skapa ersättningsinstanser på andra noder. Service Fabric fortsätter att skapa ersättningar tills tjänsten är tillbaka till önskat instansantal.

Anta till exempel att den tillståndslösa tjänsten har värdet `InstanceCount` -1. Det här värdet innebär att en instans ska köras på varje nod i klustret. Om vissa av dessa instanser misslyckas identifierar Service Fabric att tjänsten inte är i önskat tillstånd och försöker skapa instanser på noderna där de saknas.

#### <a name="stateful-services"></a>Statskänsliga tjänster
Det finns två typer av tillståndskänsliga tjänster:
- Tillståndskänslig med kvarstående tillstånd.
- Tillståndskänslig med icke-kvarstående tillstånd. (Tillståndet lagras i minnet.)

Återställning från fel på en tillståndskänslig tjänst beror på vilken typ av tillståndskänslig tjänst, hur många repliker tjänsten hade och hur många repliker som misslyckades.

I en tillståndskänslig tjänst replikeras inkommande data mellan repliker (den primära och eventuella aktiva sekundärfiler). Om en majoritet av replikerna får data betraktas data *som kvorum.* (För fem repliker kommer tre att vara kvorum.) Detta innebär att det när som helst kommer det att finnas åtminstone ett kvorum av repliker med de senaste uppgifterna. Om repliker misslyckas (säg två av fem), kan vi använda kvorumvärdet för att beräkna om vi kan återhämta oss. (Eftersom de återstående tre av fem repliker fortfarande är uppe, är det garanterat att minst en replik kommer att ha fullständiga data.)

När kvorum av repliker misslyckas, är partitionen förklaras vara i ett *kvorum förlusttillstånd.* Säg att en partition har fem repliker, vilket innebär att minst tre garanteras ha fullständiga data. Om kvorum (tre av fem) repliker misslyckas, kan Service Fabric inte avgöra om de återstående replikerna (två av fem) har tillräckligt med data för att återställa partitionen. I de fall där Service Fabric upptäcker kvorumförlust är dess standardbeteende att förhindra ytterligare skrivningar till partitionen, deklarera kvorumförlust och vänta på att kvorumet för repliker ska återställas.

Avgöra om en katastrof inträffade för en tillståndskänslig tjänst och sedan hantera den följer tre steg:

1. Avgöra om det har skett kvorum förlust eller inte.
   
   Kvorumförlust förklaras när en majoritet av replikerna av en tillståndskänslig tjänst är nere samtidigt.
2. Avgöra om kvorumförlusten är permanent eller inte.
   
   För det mesta är fel övergående. Processer startas om, noder startas om, virtuella datorer startas om och nätverkspartitioner läker. Ibland är dock misslyckanden permanenta. Om felen är permanenta eller inte beror på om den tillståndskänsliga tjänsten kvarstår sitt tillstånd eller om den bara behåller det i minnet: 
   
   - För tjänster utan kvarstående tillstånd resulterar ett fel i kvorum eller flera av repliker _omedelbart_ i permanent kvorumförlust. När Service Fabric upptäcker kvorumförlust i en tillståndskänslig icke-beständig tjänst fortsätter den omedelbart till steg 3 genom att deklarera (potentiell) dataförlust. Att fortsätta med dataförlust är vettigt eftersom Service Fabric vet att det inte är någon idé att vänta på att replikerna ska komma tillbaka. Även om de återställs kommer data att gå förlorade på grund av tjänstens icke-kvarstående karaktär.
   - För tillståndskänsliga beständiga tjänster, ett fel i ett kvorum eller flera av repliker orsakar Service Fabric att vänta på replikerna att komma tillbaka och återställa kvorum. Detta resulterar i ett avbrott i tjänsten för alla _skrivningar_ till den berörda partitionen (eller "replikuppsättningen") av tjänsten. Läsning kan dock fortfarande vara möjligt med minskade konsekvensgarantier. Standardtiden som Service Fabric väntar på att kvorumet ska återställas är *oändlig*, eftersom ett förfarande är en (potentiell) dataförlusthändelse och medför andra risker. Det innebär att Service Fabric inte går vidare till nästa steg om inte en administratör vidtar åtgärder för att deklarera dataförlust.
3. Avgöra om data går förlorade och återställa från säkerhetskopior.

   Om kvorumförlust har deklarerats (antingen automatiskt eller genom administrativa åtgärder) går Service Fabric och tjänsterna vidare till att avgöra om data verkligen har förlorats. Vid denna punkt, Service Fabric vet också att de andra replikerna inte kommer tillbaka. Det var det beslut som fattades när vi slutade vänta på att kvorumförlusten skulle lösa sig. Det bästa tillvägagångssättet för tjänsten är oftast att frysa och vänta på särskilda administrativa insatser.
   
   När Service Fabric `OnDataLossAsync` anropar metoden beror det alltid på _misstänkt_ dataförlust. Service Fabric säkerställer att det här anropet levereras till den _bästa_ återstående repliken. Detta är den replik som har gjort störst framsteg. 
   
   Anledningen till att vi alltid säger _misstänkt_ dataförlust är att det är möjligt att den återstående repliken har alla samma tillstånd som den primära gjorde när kvorum förlorades. Men utan det tillståndet att jämföra det med, det finns inget bra sätt för Service Fabric eller operatörer att veta säkert.     
   
   Så vad gör en `OnDataLossAsync` typisk implementering av metoden göra?
   1. Implementeringsloggarna `OnDataLossAsync` som har utlösts och utlöser alla nödvändiga administrativa aviseringar.
   1. Vanligtvis pausar implementeringen och väntar på att ytterligare beslut och manuella åtgärder ska vidtas. Detta beror på att även om säkerhetskopior är tillgängliga, kan de behöva förberedas. 
   
      Om till exempel två olika tjänster samordnar information kan dessa säkerhetskopior behöva ändras för att säkerställa att informationen som dessa två tjänster bryr sig om efter återställningen är konsekvent när återställningen sker. 
   1. Ofta finns det några andra telemetri eller avgassystem från tjänsten. Dessa metadata kan finnas i andra tjänster eller i loggar. Den här informationen kan användas vid behov för att avgöra om det fanns några samtal som tagits emot och bearbetats vid den primära som inte fanns i säkerhetskopian eller replikerades till den här repliken. Dessa anrop kan behöva spelas upp eller läggas till i säkerhetskopian innan återställning är möjlig.  
   1. Implementeringen jämför den återstående replikens tillstånd med det som finns i alla tillgängliga säkerhetskopior. Om du använder Service Fabric-tillförlitliga samlingar finns det [verktyg och processer](service-fabric-reliable-services-backup-restore.md) tillgängliga för detta. Målet är att se om tillståndet i repliken är tillräckligt och se vad säkerhetskopian kan saknas.
   1. När jämförelsen är klar och när återställningen har slutförts (om det behövs) ska servicekoden **returneras om** några tillståndsändringar har gjorts. Om repliken fastställde att den var den bästa tillgängliga kopian av tillståndet och inte gjorde några ändringar returnerar koden **false**. 
   
      Värdet **true** anger att _alla andra_ återstående repliker nu kan vara oförenliga med den här. De kommer att släppas och byggas om från den här repliken. Ett värde av **false** anger att inga tillståndsändringar har gjorts, så att de andra replikerna kan behålla det de har. 

Det är mycket viktigt att tjänstförfattare övar potentiella scenarier för dataförlust och fel innan tjänster distribueras i produktion. För att skydda mot risken för dataförlust är det viktigt att regelbundet [säkerhetskopiera tillståndet](service-fabric-reliable-services-backup-restore.md) för någon av dina tillståndskänsliga tjänster till ett geouppsagbart arkiv. 

Du måste också se till att du har möjlighet att återställa tillståndet. Eftersom säkerhetskopieringar av många olika tjänster tas vid olika tidpunkter måste du se till att dina tjänster efter en återställning har en konsekvent bild av varandra. 

Tänk dig till exempel en situation där en tjänst genererar ett nummer och lagrar det och sedan skickar den till en annan tjänst som också lagrar den. Efter en återställning kan du upptäcka att den andra tjänsten har numret men den första inte, eftersom säkerhetskopian inte innehöll den åtgärden.

Om du upptäcker att de återstående replikerna är otillräckliga för att fortsätta i ett scenario med dataförlust och du inte kan rekonstruera tjänsttillståndet från telemetri eller avgassystem, avgör frekvensen för dina säkerhetskopior ditt bästa möjliga återställningspunktsmål (RPO). Service Fabric innehåller många verktyg för att testa olika felscenarier, inklusive permanent kvorum och dataförlust som kräver återställning från en säkerhetskopia. Dessa scenarier ingår som en del av testabilityverktygen i Service Fabric, som hanteras av felanalystjänsten. Mer information om dessa verktyg och mönster finns i [Introduktion till felanalystjänsten](service-fabric-testability-overview.md). 

> [!NOTE]
> Systemtjänster kan också drabbas av kvorumförlust. Effekten är specifik för tjänsten i fråga. Kvorumförlust i namngivningstjänsten påverkar till exempel namnmatchning, medan kvorumförlust i redundanshanteraren-tjänsten blockerar nya tjänstskapande och redundans. 
> 
> Service Fabric-systemtjänsterna följer samma mönster som dina tjänster för tillståndshantering, men vi rekommenderar inte att du försöker flytta dem från kvorumförlust och till potentiell dataförlust. I stället rekommenderar vi att du [söker support](service-fabric-support.md) för att hitta en lösning som är inriktad på din situation. Det är oftast att föredra att helt enkelt vänta tills ner repliker tillbaka.
>

#### <a name="troubleshooting-quorum-loss"></a>Felsökning av kvorumförlust

Repliker kan vara nere periodvis på grund av ett tillfälligt fel. Vänta en tid när Service Fabric försöker ta upp dem. Om repliker har varit nere i mer än en förväntad varaktighet följer du dessa felsökningsåtgärder:
- Repliker kan krascha. Kontrollera hälsorapporter på repliknivå och dina programloggar. Samla kraschdumpar och vidta nödvändiga åtgärder för att återställa.
- Replikprocessen kan ha blivit svarar inte. Kontrollera dina programloggar för att kontrollera detta. Samla processdumpar och stoppa sedan processen som inte svarar. Service Fabric kommer att skapa en ersättningsprocess och kommer att försöka få repliken tillbaka.
- Noder som är värd för replikerna kan vara nere. Starta om den underliggande virtuella datorn för att få upp noderna.

Ibland kanske det inte går att återställa repliker. Enheterna har till exempel misslyckats eller så svarar inte datorerna fysiskt. I dessa fall måste Service Fabric bli tillsagd att inte vänta på replikåterställning.

Använd *inte* dessa metoder om potentiell dataförlust är oacceptabel för att få tjänsten online. I så fall bör alla ansträngningar göras för att återställa fysiska maskiner.

Följande åtgärder kan leda till dataförlust. Kolla innan du följer dem.
   
> [!NOTE]
> Det är _aldrig_ säkert att använda dessa metoder annat än på ett riktat sätt mot specifika partitioner. 
>

- Använd `Repair-ServiceFabricPartition -PartitionId` API:et eller `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API:et. Med det här API:et kan du ange ID:et för partitionen för att flytta från kvorumförlust och till potentiell dataförlust.
- Om klustret stöter på frekventa fel som gör att tjänster går in i kvorumförlusttillstånd och potentiell _dataförlust är acceptabelt_kan det hjälpa tjänsten att återställas automatiskt genom att ange ett lämpligt [kvorumLossWaitDuration-värde.](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) Service Fabric väntar på `QuorumLossWaitDuration` det angivna värdet (standardvärdet är oändligt) innan återställningen utförs. Vi rekommenderar *inte* den här metoden eftersom den kan orsaka oväntade dataförluster.

## <a name="availability-of-the-service-fabric-cluster"></a>Tillgänglighet för service fabric-klustret
I allmänhet är Service Fabric-klustret en mycket distribuerad miljö utan enstaka felpunkter. Ett fel på en nod orsakar inte tillgänglighets- eller tillförlitlighetsproblem för klustret, främst på grund av att Service Fabric-systemtjänsterna följer samma riktlinjer som angavs tidigare. Det vill säga, de kör alltid med tre eller flera repliker som standard, och systemtjänster som är tillståndslösa körs på alla noder. 

De underliggande nätverks- och felidentifieringslagren för Service Fabric är helt distribuerade. De flesta systemtjänster kan återskapas från metadata i klustret eller veta hur de ska synkronisera om sitt tillstånd från andra platser. Tillgängligheten för klustret kan äventyras om systemtjänster hamnar i kvorumförlustsituationer som de som beskrivits tidigare. I dessa fall kanske du inte kan utföra vissa åtgärder i klustret (som att starta en uppgradering eller distribuera nya tjänster), men själva klustret är fortfarande uppe. 

Tjänster i ett kluster som körs fortsätter att köras under dessa förhållanden om de inte kräver skrivningar till systemtjänsterna för att fortsätta fungera. Om Redundanshanteraren till exempel är i kvorumförlust fortsätter alla tjänster att köras. Men alla tjänster som misslyckas kan inte startas om automatiskt, eftersom detta kräver medverkan av Redundanhanteraren. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Fel i ett datacenter eller en Azure-region
I sällsynta fall kan ett fysiskt datacenter bli tillfälligt otillgängligt från strömavbrott eller nätverksanslutning. I dessa fall är dina Service Fabric-kluster och tjänster i det datacentret eller Azure-regionen inte tillgängliga. Dina _data bevaras_dock . 

För kluster som körs i Azure kan du visa uppdateringar om avbrott på [azure-statussidan][azure-status-dashboard]. I den högst osannolika händelsen att ett fysiskt datacenter är helt eller delvis förstört kan alla Service Fabric-kluster som finns där, eller tjänsterna inuti dem, gå förlorade. Den här förlusten inkluderar alla tillstånd som inte säkerhetskopieras utanför det datacentret eller regionen.

Det finns två olika strategier för att överleva det permanenta eller varaktiga felet i ett enda datacenter eller en enda region: 

- Kör separata Service Fabric-kluster i flera sådana regioner och använd någon mekanism för redundans och redundans mellan dessa miljöer. Den här typen av aktiv/aktiv/aktiv/passiv modell kräver ytterligare hanterings- och driftskod. Den här modellen kräver också samordning av säkerhetskopior från tjänsterna i ett datacenter eller en region så att de är tillgängliga i andra datacenter eller regioner när en misslyckas. 
- Kör ett enda Service Fabric-kluster som sträcker sig över flera datacenter eller regioner. Den minsta konfigurationen som stöds för den här strategin är tre datacenter eller regioner. Det rekommenderade antalet regioner eller datacenter är fem. 
  
  Den här modellen kräver en mer komplex klustertopologi. Fördelen är dock att fel på ett datacenter eller en region konverteras från en katastrof till ett normalt fel. Dessa fel kan hanteras av de mekanismer som fungerar för kluster inom en enda region. Feldomäner, uppgraderingsdomäner och regler för placering av service fabric säkerställer att arbetsbelastningar distribueras så att de tolererar normala fel. 
  
  Mer information om principer som kan hjälpa till att driva tjänster i den här typen av kluster finns i [Placeringsprinciper för Service Fabric-tjänster](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Slumpmässiga fel som leder till klusterfel
Service Fabric har begreppet *frönoder*. Dessa är noder som upprätthåller tillgängligheten för det underliggande klustret. 

Seed-noder hjälper till att säkerställa att klustret stannar upp genom att upprätta lån med andra noder och fungera som tiebreakers under vissa typer av fel. Om slumpmässiga fel tar bort en majoritet av seed-noderna i klustret och de inte kommer tillbaka snabbt stängs klustret av automatiskt. Klustret misslyckas sedan. 

I Azure hanterar Service Fabric Resource Provider klusterkonfigurationer för service fabric. Som standard distribuerar Resursprovidern seed-noder över fel- och uppgraderingsdomäner för den *primära nodtypen*. Om den primära nodtypen är markerad som silver- eller guldhållbarhet, när du tar bort en frönod (antingen genom skalning i din primära nodtyp eller genom att manuellt ta bort den), försöker klustret att befordra en annan icke-frönod från den primära nodtypens tillgängliga kapacitet. Det här försöket misslyckas om du har mindre tillgänglig kapacitet än vad klustertillförlitlighetsnivån kräver för den primära nodtypen.

I både fristående Service Fabric-kluster och Azure är den primära nodtypen den som kör fröna. När du definierar en primär nodtyp drar Service Fabric automatiskt nytta av antalet noder som tillhandahålls genom att skapa upp till nio frönoder och sju repliker av varje systemtjänst. Om en uppsättning slumpmässiga fel tar ut en majoritet av dessa repliker samtidigt, kommer systemtjänsterna att ange kvorumförlust. Om en majoritet av frönoderna går förlorade stängs klustret av strax efter.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du simulerar olika fel med hjälp av [testability ram](service-fabric-testability-overview.md).
- Läs andra resurser för haveriberedskap och hög tillgänglighet. Microsoft har publicerat en stor mängd vägledning om dessa ämnen. Även om vissa av dessa resurser refererar till specifika tekniker för användning i andra produkter innehåller de många allmänna metodtips som du kan använda i samband med Service Fabric:
  - [Tillgänglighetschecklista](/azure/architecture/checklist/resiliency-per-service)
  - [Utföra en borr för haveriberedskap](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Haveriberedskap och hög tillgänglighet för Azure-program][dr-ha-guide]
- Läs mer om [supportalternativ för Service Fabric.](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
