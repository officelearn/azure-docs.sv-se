---
title: Återställning av Azure Service Fabric haveri beredskap
description: Azure Service Fabric erbjuder de funktioner som krävs för att hantera alla typer av katastrofer. Den här artikeln beskrivs vilka typer av katastrofer som kan uppstå och hur du hanterar dem.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f23624dd0be1e700731e3f5a63c8cd7a00ec4e16
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458058"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Haveri beredskap i Azure Service Fabric
En viktig del av att leverera hög tillgänglighet säkerställer att tjänsterna kan överleva alla olika typer av problem. Detta är särskilt viktigt för problem som är oplanerade och utanför din kontroll. I den här artikeln beskrivs några vanliga fel lägen som kan vara katastrofer om de inte är modellerade och hanterade på rätt sätt. Den diskuterar också åtgärder och åtgärder som ska vidtas om en olycka ändå har skett. Målet är att begränsa eller eliminera risken för avbrott eller data förlust när de inträffar, planeras eller annars inträffar.

## <a name="avoiding-disaster"></a>Undvika katastrof
Service Fabricens primära mål är att hjälpa dig att modellera både din miljö och dina tjänster på ett sådant sätt att vanliga typer av fel inte är katastrofer. 

I allmänhet finns det två typer av haveri-/haveri scenarier:

1. Maskin-eller program varu fel
2. Drift fel

### <a name="hardware-and-software-faults"></a>Maskin-och program varu fel
Maskin-och program varu fel är oförutsägbara. Det enklaste sättet att överleva fel är att köra fler kopior av tjänsten som sträcker sig över gränserna för maskin-eller program varu fel. Om din tjänst till exempel bara körs på en viss dator, är det ett haveri för den tjänsten. Det enkla sättet att undvika den här katastrofen är att se till att tjänsten faktiskt körs på flera datorer. Testning är också nödvändigt för att säkerställa att en dator Miss lyckas med att inte avbryta tjänsten som körs. Kapacitets planeringen säkerställer att en ersättnings instans kan skapas någon annan stans och att den minskade kapaciteten inte överbelastar de återstående tjänsterna. Samma mönster fungerar oavsett vad du försöker att undvika. Till exempel. Om du är orolig över ett SAN-nätverk kan du köra flera San-nätverk. Om du är orolig över förlusten av en server ställning kan du köra flera rack. Om du oroar dig för att förlora data Center bör tjänsten köras i flera Azure-regioner eller data Center. 

När du kör i den här typen av utsträckt läge, omfattas du fortfarande av vissa typer av samtidiga misslyckanden, men ett och flera problem av en viss typ (t. ex. en enskild virtuell dator eller nätverks länk Miss lyckas) hanteras automatiskt (och inte längre en "katastrof"). Service Fabric innehåller många mekanismer för att expandera klustret och hantera att placera Felaktiga noder och tjänster tillbaka. Service Fabric kan också köra många instanser av dina tjänster för att undvika att dessa typer av oplanerade haverier kan omvandlas till verkliga haverier.

Det kan finnas orsaker till varför det inte är möjligt att köra en distribution som är tillräckligt stor för att täcka över felen. Det kan till exempel ta fler maskin varu resurser än vad du är villig att betala i förhållande till risken för problem. Vid hantering av distribuerade program kan det vara så att ytterligare kommunikations hopp eller kostnader för tillståndsmigrering mellan geografiska avstånd orsakar en oacceptabel fördröjning. Den här raden ritas annorlunda för varje program. För fel i program varan kan felet vara i den tjänst som du försöker skala. I det här fallet kan fler kopior inte förhindra katastrofen eftersom fel tillståndet korreleras över alla instanser.

### <a name="operational-faults"></a>Drift fel
Även om din tjänst sträcker sig över hela världen med många uppsägningar, kan den fortfarande uppleva katastrofal-händelser. Om någon till exempel av misstag konfigurerar om DNS-namnet för tjänsten eller tar bort det direkt. Anta till exempel att du hade en tillstånds känslig Service Fabric-tjänst och att någon har tagit bort tjänsten av misstag. Om det inte finns någon annan minskning är den tjänsten och hela det tillstånd den hade. Dessa typer av drift haverier ("Hoppsan") kräver olika åtgärder och återställnings steg än vanliga oplanerade haverier. 

Det bästa sättet att undvika dessa typer av drift fel är att
1. begränsa drifts åtkomst till miljön
2. strikt granskning av farliga åtgärder
3. införa automatisering, förhindra manuella eller out-of-band-ändringar och validera specifika ändringar mot den faktiska miljön innan du gör dem
4. Se till att de destruktiva åtgärderna är "Soft". Mjuka åtgärder börjar inte gälla omedelbart eller kan inte utföras inom en viss tids period

Service Fabric innehåller några mekanismer för att förhindra drift fel, till exempel att tillhandahålla [rollbaserad](service-fabric-cluster-security-roles.md) åtkomst kontroll för kluster åtgärder. De flesta av dessa drift fel kräver dock organisatoriska ansträngningar och andra system. Service Fabric tillhandahåller en mekanism för kvarvarande drifts fel, främst säkerhets kopiering och återställning för tillstånds känsliga tjänster.

## <a name="managing-failures"></a>Hantera problem
Målet med Service Fabric är nästan alltid automatisk hantering av problem. För att kunna hantera vissa typer av problem måste dock tjänsterna ha ytterligare kod. Andra typer av problem bör _inte_ åtgärdas automatiskt på grund av orsaker till säkerhet och affärs kontinuitet. 

### <a name="handling-single-failures"></a>Hantera enskilda problem
Enskilda datorer kan inte utföras av alla typer av orsaker. Några av dessa är maskin varu orsaker, t. ex. strömförsörjning och nätverks maskin varu fel. Andra problem finns i program varan. Dessa omfattar även problem med själva operativ systemet och själva tjänsten. Service Fabric identifierar automatiskt de här typerna av fel, inklusive fall där datorn blir isolerad från andra datorer på grund av nätverks problem.

Oavsett vilken typ av tjänst som körs kan en enda instans utföras under drift stopp för den tjänsten om den enskilda kopian av koden Miss lyckas av någon anledning. 

För att kunna hantera ett enskilt haveri är det enklast att se till att dina tjänster körs på fler än en nod som standard. För tillstånds lösa tjänster kan du utföra detta genom att ha en `InstanceCount` större än 1. För tillstånds känsliga tjänster är den minsta rekommendationen alltid en `TargetReplicaSetSize` och `MinReplicaSetSize` minst 3. Om du kör fler kopior av Service koden ser du till att din tjänst kan hantera alla enskilda problem automatiskt. 

### <a name="handling-coordinated-failures"></a>Hantera koordinerade haverier
Koordinerade fel kan inträffa i ett kluster på grund av planerade eller oplanerade infrastruktur fel och ändringar, eller planerade program varu ändringar. Service Fabric modeller infrastruktur zoner som upplever koordinerade fel som fel domäner. Områden som kommer att uppleva koordinerade program varu ändringar modelleras som uppgraderings domäner. Mer information om fel-och uppgraderings domäner finns i [det här dokumentet](service-fabric-cluster-resource-manager-cluster-description.md) som beskriver kluster sto pol Ogin och definition.

Som standard anses Service Fabric fel-och uppgraderings domäner när du planerar var tjänsterna ska köras. Som standard försöker Service Fabric se till att dina tjänster körs över flera fel-och uppgraderings domäner, så om planerade eller oplanerade ändringar gör att tjänsterna är tillgängliga. 

Anta till exempel att fel på en ström källa orsakar att en rack av datorer Miss lyckas samtidigt. Om flera kopior av tjänsten som kör förlusten av många datorer i fel domän fel bara förvandlas till ett annat exempel på enstaka fel för en viss tjänst. Det är därför viktigt att hantera fel domäner för att säkerställa hög tillgänglighet för dina tjänster. När du kör Service Fabric i Azure hanteras fel domäner automatiskt. I andra miljöer kanske de inte är det. Om du skapar egna kluster lokalt, måste du kartlägga och planera din feldomän-layout korrekt.

Uppgraderings domäner är användbara för modellerings områden där program vara ska uppgraderas på samma gång. Därför definierar uppgraderings domäner ofta gränserna där program varan tas offline under planerade uppgraderingar. Uppgraderingar av både Service Fabric och dina tjänster följer samma modell. Mer information om löpande uppgraderingar, uppgraderings domäner och Service Fabric hälso modell som hjälper till att förhindra oönskade ändringar från att påverka klustret och tjänsten finns i följande dokument:

 - [Program uppgradering](service-fabric-application-upgrade.md)
 - [Själv studie kurs om program uppgradering](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric hälso modell](service-fabric-health-introduction.md)

Du kan visualisera layouten för klustret med hjälp av kluster kartan som finns i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![noder sprids över fel domäner i Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Att utforma fel, löpande uppgraderingar, köra många instanser av Service koden och tillståndet, placerings regler för att säkerställa att dina tjänster körs i fel-och uppgraderings domäner, och inbyggd hälso övervakning är bara **några** av de funktioner som Service Fabric tillhandahåller för att undvika vanliga drifts problem och fel i haverier. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Hantera samtidiga maskinvaru-eller program varu problem
Ovan har vi talat om enskilda haverier. Som du kan se är det enkelt att hantera både tillstånds lösa och tillstånds känsliga tjänster genom att hålla fler kopior av koden (och tillstånd) som körs i fel-och uppgraderings domäner. Flera samtidiga slumpmässiga problem kan också inträffa. Detta är mer sannolikt att leda till en faktisk katastrof.


### <a name="random-failures-leading-to-service-failures"></a>Slumpmässiga problem som leder till tjänst haverier
Anta att tjänsten hade en `InstanceCount` på 5 och att flera noder som kör instanserna på samma gång. Service Fabric svarar genom att automatiskt skapa ersättnings instanser på andra noder. Det fortsätter att skapa ersättningar tills tjänsten är tillbaka till det önskade instans antalet. Anta till exempel att det fanns en tillstånds lös tjänst med en `InstanceCount`på-1, vilket innebär att den körs på alla giltiga noder i klustret. Anta att några av dessa instanser inte kunde köras. I det här fallet Service Fabric meddelanden om att tjänsten inte har önskat tillstånd och försöker skapa instanser på noder där de saknas. 

För tillstånds känsliga tjänster beror situationen på om tjänsten har sparat tillstånd eller inte. Det beror också på hur många repliker tjänsten hade och hur många fel som har misslyckats. Avgöra om en katastrof har inträffat för en tillstånds känslig tjänst och hantera den i följande tre steg:

1. Avgöra om det har uppstått ett kvorum eller inte
   - En förlust av kvorum är när som helst en majoritet av de repliker av en tillstånds känslig tjänst som finns på samma tidpunkt, inklusive den primära.
2. Avgöra om kvorumet kan försvinna permanent eller inte
   - Det mesta av tiden är att felen är tillfälliga. Processerna startas om, noderna startas om, virtuella datorer återstartas, nätverks partitioner laga. Ibland är felen permanenta. 
     - För tjänster utan beständig status uppstår ett problem med ett kvorum eller flera repliker _direkt_ i permanent kvorum förlust. När Service Fabric identifierar kvorum i en tillstånds känslig tjänst, fortsätter den omedelbart till steg 3 genom att deklarera (potential) data förlust. Att gå vidare till data förlust är meningsfullt eftersom Service Fabric vet att det inte finns någon tidpunkt i väntan på att replikerna ska komma tillbaka, eftersom även om de återställdes, skulle de vara tomma.
     - För tillstånds känsliga tjänster innebär ett fel på ett kvorum eller flera repliker att Service Fabric starta väntar på att replikerna ska komma tillbaka och återställa kvorum. Detta resulterar i avbrott i tjänsten för alla _skrivningar_ till den berörda partitionen (eller "replik uppsättningen") för tjänsten. Läsningar kan dock fortfarande vara möjliga med minskad konsekvens garanti. Standard tiden som Service Fabric väntar på att kvorum ska återställas är oändlig, eftersom det är en (potentiell) data förlust händelse och medför andra risker. Att åsidosätta standard `QuorumLossWaitDuration` svärdet är möjligt, men rekommenderas inte. I stället för den här gången bör alla ansträngningar göras för att återställa ned-replikerna. Detta kräver att noderna som är nere säkerhets kopie ras och säkerställer att de kan montera om de enheter där de har sparat det lokala beständiga läget. Om återställningen av kvorum orsakas av ett process haveri försöker Service Fabric automatiskt återskapa processerna och starta om replikerna inuti dem. Om detta Miss lyckas, Service Fabric rapporterar hälso fel. Om dessa kan lösas kommer replikerna normalt tillbaka. Ibland kan inte replikerna tas tillbaka. Enheterna kan till exempel ha misslyckats eller så har datorerna fysiskt förstörts. I dessa fall har vi nu ett permanent kvorum för förlorade händelser. Om du vill att Service Fabric ska sluta vänta på att de båda replikerna ska gå tillbaka måste en kluster administratör bestämma vilka partitioner i vilka tjänster som påverkas och anropa `Repair-ServiceFabricPartition -PartitionId`-eller `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)`-API: et.  Med detta API kan du ange ID: t för partitionen som ska flyttas från QuorumLoss och till potentiella DataLoss.

   > [!NOTE]
   > Det är _aldrig_ säkert att använda detta API förutom på ett riktat sätt mot specifika partitioner. 
   >

3. Avgöra om det finns faktisk data förlust och återställning från säkerhets kopior
   - När Service Fabric anropar `OnDataLossAsync`-metoden är det alltid på grund av _misstänkt_ data förlust. Service Fabric garanterar att anropet levereras till den _bästa_ återstående repliken. Detta är den replik som har förloppet. Orsaken till att det alltid finns _misstänkt_ data förlust är att det är möjligt att den kvarvarande repliken faktiskt har samma tillstånd som den primära startade när den gick ned. Men utan det läget för att jämföra det, finns det inget bra sätt för Service Fabric eller operatörer att känna till. I det här läget vet Service Fabric också att de andra replikerna inte kommer tillbaka. Det var beslutet som fattades när vi slutade vänta på att kvorumet skulle lösas. Den bästa åtgärden för tjänsten är vanligt vis att frysa och vänta på en speciell administrativ åtgärd. Vad gör en typisk implementering av `OnDataLossAsync`-metoden?
   - Börja med att logga `OnDataLossAsync` har utlösts och starta eventuella nödvändiga administrativa aviseringar.
   - Normalt i det här läget för att pausa och vänta på ytterligare beslut och manuella åtgärder som ska vidtas. Detta beror på att även om säkerhets kopieringar är tillgängliga kan de behöva förberedas. Om till exempel två olika tjänster koordinerar information kan dessa säkerhets kopieringar behöva ändras för att säkerställa att när återställningen sker att den information som de två tjänsterna bryr sig om är konsekvent. 
   - Det finns ofta ytterligare telemetri eller avgaser från tjänsten. Dessa metadata kan finnas i andra tjänster eller i loggar. Den här informationen kan användas för att avgöra om det fanns några anrop som tagits emot och bearbetats på den primära som inte fanns i säkerhets kopian eller repliker ATS till den aktuella repliken. De kan behöva spelas upp eller läggas till i säkerhets kopian innan återställningen är möjlig.  
   - Jämförelser av den återstående replikens tillstånd till den som finns i alla säkerhets kopior som är tillgängliga. Om du använder Service Fabric pålitliga samlingar finns det verktyg och processer som är tillgängliga för detta, som beskrivs i [den här artikeln](service-fabric-reliable-services-backup-restore.md). Målet är att se om tillstånd i repliken är tillräckligt, eller också vad säkerhets kopieringen kan saknas.
   - När jämförelsen är klar, och om nödvändigt återställningen har slutförts, ska tjänst koden returnera True om några tillstånds ändringar gjordes. Om repliken har fastställt att det var den bästa tillgängliga kopian av statusen och inga ändringar har gjorts, returnerar du falskt. True anger att _andra_ återstående repliker kan nu vara inkonsekventa med den här. De kommer att släppas och återskapas från den här repliken. FALSE anger att inga tillstånds ändringar gjordes, så de andra replikerna kan behålla det du har. 

Det är mycket viktigt att tjänst författarna kan öva på potentiell data förlust och felaktiga scenarier innan tjänsterna distribueras i produktion. För att skydda mot risken för data förlust är det viktigt att regelbundet [säkerhetskopiera statusen](service-fabric-reliable-services-backup-restore.md) för alla tillstånds känsliga tjänster till en Geo-redundant lagring. Du måste också se till att du kan återställa den. Eftersom säkerhets kopieringar av många olika tjänster görs vid olika tidpunkter, måste du se till att efter en återställning av dina tjänster har en enhetlig vy av varandra. Anta till exempel en situation där en tjänst genererar ett nummer och lagrar den och skickar den sedan till en annan tjänst som också lagrar den. Efter en återställning kan du upptäcka att den andra tjänsten har numret, men det första inte innebär att det inte finns någon säkerhets kopia.

Om du tar reda på att de återstående replikerna inte är tillräckliga för att fortsätta från i ett data förlust scenario och du inte kan konstruera om tjänst status från telemetri eller avgas, bestämmer säkerhets kopierings frekvensen det bästa möjliga återställnings punkt målet. Service Fabric innehåller många verktyg för att testa olika typer av avbrott, inklusive permanent kvorum och data förlust som kräver återställning från en säkerhets kopia. Dessa scenarier ingår som en del av Service Fabrics verktyg för testning, som hanteras av fel analys tjänsten. Mer information om verktygen och mönstren finns [här](service-fabric-testability-overview.md). 

> [!NOTE]
> System tjänster kan också drabbas av kvorum förlust, med inverkan på den aktuella tjänsten i fråga. Till exempel kan kvorumet i namngivnings tjänsten påverkar namn matchningen, medan kvorumet i tjänsten failover Manager blockerar skapande av nya tjänster och redundans. Medan Service Fabric system tjänster följer samma mönster som dina tjänster för tillstånds hantering, rekommenderar vi inte att du försöker flytta dem bort från kvorumet och till potentiell data förlust. Rekommendationen är i stället att [söka efter support](service-fabric-support.md) för att fastställa en lösning som är riktad mot din specifika situation.  Vanligt vis är det bättre att bara vänta tills de båda replikerna har returnerats.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Service Fabric klustrets tillgänglighet
I allmänhet är Service Fabric själva klustret en mycket distribuerad miljö utan några enskilda fel punkter. Ett fel på en nod kan inte orsaka tillgänglighets-eller Tillförlitlighets problem för klustret, främst på grund av att Service Fabric system tjänster följer samma rikt linjer som tidigare: de körs alltid med tre eller fler repliker som standard och dessa system tjänster som är tillstånds lösa körs på alla noder. Underliggande Service Fabric nätverks-och identifierings lager är fullständigt distribuerade. De flesta system tjänster kan återskapas från metadata i klustret, eller veta hur de synkroniseras om från andra platser. Tillgängligheten för klustret kan bli komprometterad om system Services får problem med att förlora kvorum, som de som beskrivs ovan. I dessa fall kanske du inte kan utföra vissa åtgärder i klustret, t. ex. Starta en uppgradering eller distribuera nya tjänster, men själva klustret är fortfarande igång. Tjänster som redan körs fortsätter att köras under dessa villkor, om de inte kräver skrivningar till system tjänsterna för att fortsätta att fungera. Om Redundanshanteraren till exempel är i en kvorum förlust, kommer alla tjänster att fortsätta att köras, men alla tjänster som Miss lyckas kommer inte att kunna startas om automatiskt, eftersom detta kräver att Redundanshanterarens medverkan. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Problem i ett Data Center eller Azure-region
I sällsynta fall kan ett fysiskt Data Center bli tillfälligt otillgängligt på grund av strömavbrott eller nätverks anslutning. I dessa fall kommer Service Fabric kluster och-tjänster i data centret eller Azure-regionen inte vara tillgängliga. _Dina data kommer dock att bevaras_. För kluster som körs i Azure kan du Visa uppdateringar om avbrott på [sidan Azure-status][azure-status-dashboard]. Om det är mycket osannolikt att ett fysiskt Data Center är delvis eller helt förstört, kan alla Service Fabric kluster som finns där eller tjänsterna i dem gå förlorade. Detta omfattar alla tillstånd som inte säkerhets kopie ras utanför data centret eller regionen.

Det finns två olika strategier för att arbeta oavbrutet i det permanenta eller varaktiga haveriet i ett enda data Center eller en region. 

1. Kör separata Service Fabric kluster i flera sådana regioner och Använd en mekanism för redundans och återställning mellan dessa miljöer. Den här sorteringen av aktiv-aktiv eller aktiv-passiv modell med flera kluster kräver ytterligare hanterings-och åtgärds kod. Detta kräver också samordning av säkerhets kopieringar från tjänsterna i ett Data Center eller en region så att de är tillgängliga i andra data Center eller regioner när ett fel uppstår. 
2. Kör ett enda Service Fabric-kluster som sträcker sig över flera data Center eller regioner. Den lägsta konfigurationen som stöds för detta är tre Data Center eller regioner. Det rekommenderade antalet regioner eller data Center är fem. Detta kräver en mer komplex kluster sto pol Ogin. Fördelen med den här modellen är dock att felet i ett Data Center eller en region konverteras från en katastrof till ett normalt haveri. Dessa fel kan hanteras av de mekanismer som fungerar för kluster inom en enda region. Fel domäner, uppgraderings domäner och Service Fabric placerings regler säkerställer att arbets belastningarna distribueras så att de tolererar vanliga fel. Mer information om principer som kan hjälpa dig att använda tjänster i den här typen av kluster finns i [placerings principer](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Slumpmässiga fel som leder till kluster fel
Service Fabric har begreppet startnoder. Detta är noder som upprätthåller tillgängligheten för det underliggande klustret. Dessa noder bidrar till att säkerställa att klustret förblir igång genom att upprätta lån med andra noder och betjäna som tiebreakers under vissa typer av nätverks fel. Om slumpmässiga fel tar bort en majoritet av startnoderna i klustret och de inte tas tillbaka, komprimeras kluster federationens ring när du har förlorat kvorum och klustret slutar fungera. I Azure hanterar Service Fabric Resource Provider Service Fabric klusterkonfigurationer, och som standard distribueras dirigeringsrouters mellan primära nodtyper för typ fel och uppgraderings domäner. Om den primära NodeType är markerad som silver eller Gold-hållbarhet, och om du tar bort en Seed-nod, antingen genom att skala i din primära NodeType eller manuellt tar bort en Seed-nod, försöker klustret befordra en annan icke-Seed-nod från den primära NodeType som är tillgänglig kapacitet och kommer att Miss förväntas om du har mindre tillgänglig kapacitet än vad klustrets Tillförlitlighets nivå kräver för den primära nodtypen.

I båda fristående Service Fabric kluster och Azure är "primär nodtyp" den som kör frön. När du definierar en typ av primär nod, kommer Service Fabric automatiskt att dra nytta av antalet noder som tillhandahålls genom att skapa upp till 9 startnoder och 7 repliker av varje system tjänster. Om en uppsättning slumpmässiga haverier tar ut en majoritet av dessa system tjänst repliker samtidigt, kommer system tjänsterna att ange kvorum, så som vi beskrivits ovan. Om en majoritet av startnoderna förloras stängs klustret strax efter.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du simulerar olika fel med hjälp av [test ramverket](service-fabric-testability-overview.md)
- Läs andra haveri beredskap och resurser med hög tillgänglighet. Microsoft har publicerat en stor mängd vägledning om dessa ämnen. Vissa av dessa dokument hänvisar till vissa tekniker för användning i andra produkter, men de innehåller många allmänna metod tips som du kan använda i Service Fabric kontexten:
  - [Tillgänglighetschecklista](/azure/architecture/checklist/resiliency-per-service)
  - [Utföra en katastrof återställnings granskning](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Haveriberedskap och hög tillgänglighet för Azure-program][dr-ha-guide]
- Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
