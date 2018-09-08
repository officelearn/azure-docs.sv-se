---
title: Azure Service Fabric-katastrofåterställning | Microsoft Docs
description: Azure Service Fabric innehåller funktioner som är nödvändiga för att hantera alla typer av katastrofer. Den här artikeln beskrivs vilka typer av katastrofer som kan uppstå och hur du hanterar dem.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 4b13d2d277721d37a6b96f6640377c875f0b5c0f
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161589"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Haveriberedskap i Azure Service Fabric
En viktig del av att leverera hög tillgänglighet är att säkerställa att tjänster kan överleva alla olika typer av fel. Detta är särskilt viktigt för fel som är oplanerade och utanför din kontroll. Den här artikeln beskriver några vanliga feltillstånd som kan vara katastrofer om inte modelleras och hanteras korrekt. Här beskrivs även åtgärder och åtgärder som ska vidtas om en katastrof har inträffat ändå. Målet är att begränsa eller eliminera risken för avbrott eller dataförluster när de uppstår fel, planerat eller i annat fall sker.

## <a name="avoiding-disaster"></a>Undvika haveriberedskap
Service Fabric Huvudsyftet är att hjälpa dig modellera både din miljö och dina tjänster så att vanliga typer av fel inte är katastrofer. 

I allmänhet finns det två typer av scenarier för haveriberedskap/misslyckade:

1. Maskin- eller fel
2. Operativa fel

### <a name="hardware-and-software-faults"></a>Maskin- och fel
Maskin- och felen är oförutsägbar. Det enklaste sättet att överleva fel körs fler kopior av tjänsten omfatta maskinvaru- eller fault-gränser. Om din tjänst körs bara på en viss dator, är fel på den en datorn en katastrof för tjänsten. Det enkla sättet att undvika den här haveriberedskap är att säkerställa att tjänsten körs faktiskt på flera datorer. Testning är också nödvändigt att säkerställa fel på en dator inte störa tjänsten som körs. Kapacitetsplanering säkerställer en ersättning-instans kan skapas på andra platser och att minskad kapacitet inte överbelasta kvarvarande tjänster. Samma mönster fungerar oavsett vad du försöker undvika fel på. Till exempel. Om du är orolig över fel på ett SAN-nätverk kan köra du över flera SAN-nätverk. Om du är orolig över förlusten av rackmonterade servrar kan köra du över flera rack. Om du oroar förlusten av datacenter, köra din tjänst i flera Azure-regioner eller Datacenter. 

Vid körning i den här typen av utsträckta läge kan du är fortfarande gäller vissa typer av samtidiga fel, men enskild och även flera fel av en viss typ (ex: en enda virtuell dator eller nätverket länk misslyckas) hanteras automatiskt (och därför inte längre en ”katastrof”). Service Fabric tillhandahåller flera mekanismer för att expandera klustret och hanterar föra misslyckade noder och tjänsters. Service Fabric kan också köra flera instanser av dina tjänster för att undvika dessa typer av oplanerade fel från att sättas i verkliga katastrofer.

Det kan finnas skälen till som kör en distribution som är tillräckligt stor för att omfatta över fel inte är möjligt. Det kan till exempel ta mer maskinvaruresurser än du kan tänka dig att betala för i förhållande till risken för fel. När du hanterar distribuerade program, kan det ytterligare kommunikation hopp eller tillståndet replikering kostar över geografiska avstånd orsaker oacceptabel svarstid. Var den här raden ska ritas skiljer sig åt för varje program. För program-eller maskinvarufel kan särskilt felet i tjänsten som du vill skala. I det här fallet förhindra inte fler kopior haveriberedskap, eftersom feltillståndet korreleras i alla instanser.

### <a name="operational-faults"></a>Operativa fel
Även om tjänsten omfattas över hela världen med många uppsägningar, kan det fortfarande uppleva och med katastrofal händelser. Exempel: om någon av misstag konfigurerar om dns-namn för tjänsten eller tar bort en gång. Till exempel anta att du hade en tillståndskänslig Service Fabric-tjänst och någon bort tjänsten av misstag. Om det inte finns några andra minskning, tjänsten och alla tillstånd den hade är nu borta. Dessa typer av operativa katastrofer kräver (”hoppsan”) olika åtgärder och steg för återställning än vanlig oplanerade fel. 

De bästa sätten att undvika dessa typer av operativa fel som avser
1. begränsa operativa åtkomst till miljön
2. strikt granskningsåtgärder farliga
3. införa automation, förhindra manuell eller utanför band ändringar och validera specifika ändringar av mot de faktiska miljön innan du tillämpa dem
4. Kontrollera att destruktiva åtgärder är ”soft”. Mjuk åtgärder börjar gälla inte omedelbart eller kan ångras inom vissa tidsintervall

Service Fabric tillhandahåller del mekanismer för att förhindra operativa fel, till exempel att ge [rollbaserad](service-fabric-cluster-security-roles.md) åtkomstkontroll för klusteråtgärder. De flesta av dessa operativa fel kräver dock arbete och andra system. Service Fabric gör tillhandahåller en mekanism för kvarvarande operativa fel, framför säkerhetskopiering och återställning för tillståndskänsliga tjänster.

## <a name="managing-failures"></a>Hantera fel
Syftet med Service Fabric är nästan alltid automatisk hantering av fel. För att hantera vissa typer av fel, måste dock tjänster ha ytterligare kod. Andra typer av fel bör _inte_ åtgärdas automatiskt på grund av säkerhets- och business continuity orsaker. 

### <a name="handling-single-failures"></a>Enkel misslyckanden
Enskilda datorer kan misslyckas av alla möjliga orsaker. Vissa av dessa är maskinvara orsaker, t.ex. strömförsörjning och nätverk maskinvarufel. Det finns andra fel i programvaran. Dessa inkluderar för faktiska operativsystemet och själva tjänsten. Service Fabric identifierar automatiskt dessa typer av fel, inklusive fall där datorn blir isolerad från andra datorer på grund av nätverksproblem.

Oavsett vilken typ av tjänsten som kör en enda instans resultat driftstopp för tjänsten om den enda kopian av koden misslyckas av någon anledning. 

För att hantera varje enskilt fel är det enklaste som du kan göra att säkerställa att dina tjänster köras på mer än en nod som standard. För tillståndslösa tjänster kan detta kan åstadkommas genom att låta en `InstanceCount` större än 1. För tillståndskänsliga tjänster minsta rekommendationen är alltid en `TargetReplicaSetSize` och `MinReplicaSetSize` minst 3. Kör fler kopior av koden för tjänsten säkerställer att din tjänst kan hantera varje enskilt fel automatiskt. 

### <a name="handling-coordinated-failures"></a>Hantering av koordinerad fel
Samordnad fel kan inträffa i ett kluster på grund av en planerade eller oplanerade infrastrukturfel och ändringar eller planerade programvaruändringar. Service Fabric-modeller infrastruktur zoner som uppstår samordnad fel som Feldomäner. Områden som kommer att uppleva samordnad programvaruändringar modelleras som uppgradera domäner. Mer information om fel- och uppgraderingsdomäner finns i [det här dokumentet](service-fabric-cluster-resource-manager-cluster-description.md) beskrivs klustertopologi och definition.

Som standard Service Fabric tar hänsyn till fel- och uppgraderingsdomäner när du planerar där dina tjänster ska köras. Som standard försöker Service Fabric se till att dina tjänster körs över flera fel- och uppgraderingsdomäner, så om planerad eller oplanerad dataändringar görs dina tjänster fortfarande är tillgängliga. 

Anta exempelvis att att fel på en strömkälla orsakar ett rack av datorer till misslyckas samtidigt. Blir bara ett annat exempel på enstaka fel för en viss tjänst med flera kopior av tjänsten som körs förlusten av många datorer i fel domän fel. Det här är anledningen till att hantera feldomäner är viktiga för att garantera hög tillgänglighet för dina tjänster. När du kör Service Fabric i Azure hanteras feldomäner automatiskt. I andra miljöer kanske de inte. Om du skapar dina egna kluster lokalt, måste du mappa och planera fel domän layouten korrekt.

Uppgraderingsdomäner är användbara för modellering områden där programvaran ska uppgraderas samtidigt. Därför definiera uppgradera domäner också ofta gränserna där programvaran är stängt under planerade uppgraderingar. Uppgraderingar av både Service Fabric och dina tjänster följer samma modell. Mer information om löpande uppgraderingar, uppgraderingsdomäner och Service Fabric hälsomodell som hjälper till att förhindra att oönskade ändringar påverkar klustret och tjänsten finns i dessa dokument:

 - [Programuppgradering](service-fabric-application-upgrade.md)
 - [Självstudier för uppgradering av program](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric-Hälsomodell](service-fabric-health-introduction.md)

Du kan visualisera layouten för ditt kluster med hjälp av klusterkarta i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![Noder som är fördelade över feldomäner i Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modellering områden för fel, löpande uppgraderingar, kör flera instanser av din tjänst kod och tillstånd, placeringsregler att se till att dina tjänster köra alla fel- och uppgraderingsdomäner och inbyggda hälsoövervakning är bara **vissa** av den funktioner som Service Fabric tillhandahåller för att förhindra att omvandla till katastrofer normala operativa problem och fel. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Hantering av samtidiga maskinvaru- eller nodfel
Senare talade vi om enda fel. Som du ser är enkla att hantera för både tillståndslösa och tillståndskänsliga tjänster genom att hålla fler kopior av kod (och tillstånd) som körs på olika fel- och uppgraderingsdomäner. Flera samtidiga slumpmässiga fel kan också inträffa. Här är mer troligt att leda till en verklig katastrof.


### <a name="random-failures-leading-to-service-failures"></a>Slumpmässiga fel som leder till inträffar
Anta att tjänsten har en `InstanceCount` 5 och flera noder instanserna alla gick inte att köra på samma gång. Service Fabric svarar genom att automatiskt skapa ersättning instanser på andra noder. Den kommer att fortsätta skapa ersättningar tills tjänsten har tillbaka till det önskade instansantalet. Ett annat exempel anta att det uppstod en tillståndslös tjänst med en `InstanceCount`-1, vilket innebär att den körs på alla giltiga noder i klustret. Anta att några av dessa instanser skulle sluta fungera. Service Fabric meddelanden i det här fallet att tjänsten inte är dess status som önskas och försöker skapa instanser på noderna där de saknas. 

För tillståndskänsliga tjänster beror situationen på om tjänsten har sparat tillstånd eller inte. Det beror också på hur många kopior som tjänsten hade och hur många misslyckades. Bedöma om en katastrof inträffade för en tillståndskänslig tjänst och hantera det följer tre steg:

1. Avgör om det har varit förlorar kvorum eller inte
 - En förlorar kvorum är helst en majoritet av replikeringar av en tillståndskänslig tjänst är nere på samma gång, inklusive primärt.
2. Avgör om förlorar kvorum är permanent eller inte
 - De flesta fall är fel tillfälligt. Processer har startats om, noder startas om, virtuella datorer är relaunched, nätverkspartitioner reparation av nodtjänst. Ibland är fel permanent. 
    - För tjänster utan sparade tillstånd, ett fel för ett kvorum eller flera av repliker resultat _omedelbart_ förlorar kvorum permanent. När Service Fabric upptäcker förlorar kvorum i ett icke-beständiga tillståndskänslig tjänst, fortsätter den genast till steg 3 genom att deklarera () förlust av data. Fortsätter att data är går förlorade naturligt eftersom Service Fabric vet att det ingen finns i väntan på att replikerna kommer tillbaka, eftersom även om de har återställts de skulle vara tom.
    - För tillståndskänsliga permanenta tjänster orsakar ett fel i ett kvorum eller flera av repliker Service Fabric för att starta väntar på att replikerna komma tillbaka och återställa kvorum. Detta resulterar i ett tjänstavbrott för någon _skriver_ till berörda partition (eller ”replikuppsättningen”) av tjänsten. Läsningar kan dock fortfarande vara möjligt med lägre konsekvensgarantier. Standard för lång tid som Service Fabric väntar för kvorum som ska återställas är oändliga, eftersom du fortsätter en (potentiella) dataförlusthändelse och har andra risker. Åsidosätter förvalda `QuorumLossWaitDuration` värde är möjligt, men rekommenderas inte. I stället för tillfället bör yttersta göra för att återställa på replikerna. Detta kräver att de noder som är nere säkerhetskopiera och att säkerställa att de kan montera om de enheter där de lagras lokalt beständiga tillståndet. Om förlorar kvorum orsakas av fel, försöker Service Fabric automatiskt återskapa processerna och starta om repliker i dem. Om det inte rapporterar problem med hälsotillståndet i Service Fabric. Om det kan vara löst gå replikerna vanligtvis sedan tillbaka. Ibland kan dock kan inte replikerna hämtas tillbaka. Till exempel enheterna kan alla har misslyckats eller datorerna förstörs fysiskt på något sätt. I dessa fall kan har vi nu en permanent kvorum dataförlust inträffat. Om du vill se Service Fabric för att stoppa väntar på replikerna ska komma tillbaka en Klusteradministratör måste bestämma vilka partitioner som tjänster som påverkas och anropa den `Repair-ServiceFabricPartition -PartitionId` eller ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API.  Detta API kan du ange ID för partitionen som ska flytta ut från QuorumLoss till potentiella dataloss.

  > [!NOTE]
  > Det är _aldrig_ säkert att använda detta API än på ett sätt som är riktade mot specifika partitioner. 
  >

3. Avgöra om det har förekommit faktiska data går förlorade och återställer från säkerhetskopior
  - När Service Fabric anropar den `OnDataLossAsync` metod, är det alltid grund av _misstänkt_ förlust av data. Service Fabric säkerställer att det här anropet levereras till den _bästa_ återstående repliken. Det här är repliken har gjort de flesta förloppet. Orsaken vi säger alltid _misstänkt_ förlust av data är att det är möjligt att återstående repliken faktiskt har alla samma tillstånd som primärt när det avslutades. Men utan att det aktuella tillståndet att jämföra den för att finns det inget bra sätt för Service Fabric och operatorer för att ta reda på. Service Fabric vet nu även andra replikerna inte kommer tillbaka. Det var beslut som fattas när vi stoppats och väntar förlorar kvorum löser sig. Bästa åtgärd för tjänsten är vanligtvis att låsa och vänta tills specifika administrativa åtgärder. Så vad gör en typisk implementering av den `OnDataLossAsync` metoden göra?
  - Logga först in som `OnDataLossAsync` har utlösts och ställa in alla nödvändiga administrativa varningar.
   - Vanligtvis nu att pausa och vänta på ytterligare beslut och manuella åtgärder som ska vidtas. Det beror på att även om säkerhetskopieringar är tillgängliga kan de behöva förberedas. Till exempel om två olika tjänster samordna information kan säkerhetskopieringarna behöva ändras för att säkerställa att när återställningen sker som informationen om dessa två tjänster försiktighet om är konsekvent. 
  - Ofta finns även vissa andra telemetri- eller avgaser från tjänsten. Dessa metadata kan finnas i andra tjänster eller i loggarna. Den här informationen kan användas krävs för att avgöra om det fanns alla anrop emot och bearbetas på primärt som fanns inte i säkerhetskopian eller replikeras till den här specifika repliken. Dessa kan behöva vara återupprepas eller lagts till i säkerhetskopian innan återställningen är möjligt.  
   - Jämförelser av de återstående repliktillståndet som eventuella säkerhetskopior som är tillgängliga. Om du använder Service Fabric tillförlitliga samlingar finns verktyg och bearbetar tillgängliga för att göra det, beskrivs i [i den här artikeln](service-fabric-reliable-services-backup-restore.md). Målet är att se om tillståndet inom repliken räcker eller också vilka säkerhetskopian kanske saknas.
  - När jämförelsen görs och om nödvändiga återställningen har slutförts, kod som ska returnera true om alla tillståndsändringar har gjorts. Om repliken fastställt att det var den bästa tillgängliga kopian av tillståndet och gjort några ändringar är returnera FALSKT. SANT anger att alla _andra_ återstående repliker kan nu vara inkonsekvent med den här. De ska släppas och återskapas från den här repliken. Falskt anger att inga tillståndsändringar har gjorts, så att andra repliker kan behålla de har. 

Det är ytterst viktigt att tjänsten författare öva potentiella data går förlorade och misslyckade scenarier innan services distribueras någonsin i produktion. För att skydda mot risk för dataförlust, är det viktigt att regelbundet [säkerhetskopiera tillståndet](service-fabric-reliable-services-backup-restore.md) för någon av dina tillståndskänsliga tjänster till ett geo-redundant lager. Du måste också se till att du har möjlighet att återställa den. Eftersom säkerhetskopior av många olika tjänster är tagna vid olika tidpunkter, måste du se till att dina tjänster har en enhetlig vy över varandra efter en återställning. Anta exempelvis att en situation där en tjänst genererar ett tal och lagrar det och skickar det till en annan tjänst som lagrar också den. Efter en återställning märker du kanske att de andra tjänsterna har många men första gör inte, eftersom det är säkerhetskopiering inte angav den åtgärden.

Om du upptäcker att återstående replik(er) är tillräckligt för att fortsätta från i dataförluster och du kan rekonstruera tjänsttillstånd från telemetri- eller avgaser, anger frekvensen för säkerhetskopieringar av din bästa möjliga mål för återställningspunkt (RPO). Service Fabric innehåller många verktyg för att testa olika felscenarier, inklusive permanenta kvorum och förlust av data som kräver återställning från en säkerhetskopia. Dessa scenarier ingår som en del av Service Fabric möjligheten att testa verktyg, hanteras av Fault Analysis Service. Mer information om dessa verktyg och mönster finns [här](service-fabric-testability-overview.md). 

> [!NOTE]
> Systemtjänster kan också bli sämre kvorum försvinner, med påverkan som är specifika för tjänsten i fråga. Exempelvis påverkar förlorar kvorum i namngivningstjänsten namnmatchning, medan förlorar kvorum i tjänsten redundans manager blockerar skapandet av ny tjänsten och redundans. När Service Fabric-systemtjänster följer samma mönster som för dina tjänster för tillståndshantering, rekommenderas inte att du använder för att flytta dem utanför förlorar kvorum och till förlust av data. Rekommendationen är i stället att [söker support](service-fabric-support.md) att fastställa en lösning som är avsedda för dina behov.  Vanligtvis är det bättre att bara vänta tills på replikerna kommer tillbaka.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Tillgängligheten för Service Fabric-kluster
Generellt sett är själva Service Fabric-klustret en miljö med inga enskilda felpunkter. Ett fel för en noderna inte orsakar tillgänglighet eller tillförlitlighetsproblem för klustret, främst eftersom Service Fabric-systemtjänster följa samma riktlinjer som angavs tidigare: de alltid körs med tre eller fler repliker som standard, och dessa system tjänster som är tillståndslösa köras på alla noder. De underliggande Service Fabric nätverks- och fel vid identifiering av lagren distribueras helt. De flesta systemtjänster kan återskapas från metadata i klustret eller vet hur man synkronisera om deras tillstånd från andra platser. Tillgängligheten för klustret kan avslöjats om systemtjänster få i kvorum förlust situationer som de som beskrivs ovan. I dessa fall kan du inte att kunna utföra vissa åtgärder på klustret som påbörjar en uppgradering eller distribuera nya tjänster, men själva klustret körs fortfarande. Tjänster på redan körs kommer fortsätta att köras i dessa villkor, såvida inte de kräver skrivningar till systemtjänster ska fortsätta att fungera. Till exempel om den Redundanshanteraren är förlorar kvorum alla tjänster fortsätter att köras, men kan inte startas om automatiskt, eftersom detta kräver medverkan av Redundanshanteraren i alla tjänster som misslyckas. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Fel i ett datacenter eller en Azure-region
I sällsynta fall kan ett fysiska Datacenter blir tillfälligt otillgänglig på grund av förlust av power nätverksanslutningar. I dessa fall kan blir dina Service Fabric-kluster och tjänster i datacenter eller Azure-region otillgänglig. Dock _dina data bevaras_. För kluster som körs i Azure kan du visa uppdateringar på avbrott på den [Azure-statussidan][azure-status-dashboard]. I händelsen mycket osannolikt att ett fysiska Datacenter helt eller delvis förstörs alla Service Fabric-kluster som finns det eller tjänsterna i dem kan gå förlorade. Detta inkluderar alla tillstånd som inte säkerhetskopieras utanför den datacenter eller en region.

Det finns två olika strategier för kvarvarande permanent eller fasta programmönster fel på ett och samma datacenter eller en region. 

1. Kör separata Service Fabric-kluster i flera regioner och använda någon mekanism för redundans och redundansväxla mellan dessa miljöer. Den här typen av flera klustermodell för aktiv-aktiv eller aktiv-passiv kräver extra kod för hantering och åtgärder. Detta kräver samordning av säkerhetskopior från tjänster i ett datacenter eller en region så att de är tillgängliga i andra Datacenter eller regioner när en misslyckas. 
2. Kör ett enda Service Fabric-kluster som sträcker sig över flera Datacenter eller regioner. Den lägsta konfigurationen som stöds för det här är tre Datacenter eller regioner. Det rekommenderade antalet regioner eller Datacenter är fem. Detta kräver en mer komplex klustertopologi. Fördelen med den här modellen är dock att fel i ett datacenter eller en region konverteras till ett vanligt fel efter en katastrof. De här felen kan hanteras av de mekanismer som fungerar för kluster inom en enda region. Feldomäner och uppgraderingsdomäner placeringsregler för Service Fabric ser till att arbetsbelastningar distribueras så att de tolerera vanliga fel. Mer information om principer som kan hjälpa dig att använda tjänster i den här typen av kluster, Läs igenom [placeringsprinciper](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Slumpmässiga fel som leder till klusterfel
Service Fabric har begreppet Startvärdesnoder. Det här är noder som underhåller tillgängligheten för underliggande klustret. Dessa noder hjälpa dig att se till att klustret förblir upp genom att upprätta lån med andra noder och fungerar som tiebreakers under vissa typer av nätverksfel. Om slumpmässiga fel ta bort en majoritet av seed-noder i klustret och de återförs inte stängs klustret automatiskt. I Azure, hanteras automatiskt Startvärdesnoder: de har distribuerats över tillgängliga feldomäner och uppgraderingsdomäner, och om en enda seed-nod har tagits bort från klustret ett annat kommer att skapas i dess ställe. 

I både fristående Service Fabric-kluster och Azure är ”primära nodtypen” det som körs på frö. När du definierar en primära nodtypen Service Fabric automatiskt att dra nytta av antalet noder som tillhandahålls genom att skapa upp till 9 startvärdesnoder och 9 repliker av var och en av systemtjänster. Om en uppsättning slumpmässiga fel tar ut en majoritet av dessa system service repliker samtidigt kan ange systemtjänster förlorar kvorum, som vi som beskrivs ovan. Om en majoritet av startvärdesnoder går förlorade kan stänger klustret strax efter.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du simulera olika fel med den [testmöjlighet framework](service-fabric-testability-overview.md)
- Läs andra resurser för haveriberedskap och hög tillgänglighet. Microsoft har publicerat en stor mängd information om dessa ämnen. Även om vissa av dessa dokument refererar till specifika tekniker för användning i andra produkter, innehåller de många allmänna metodtips som du kan använda i kontexten Service Fabric:
  - [Tillgänglighetschecklista](../best-practices-availability-checklist.md)
  - [Utföra ett programåterställningstest](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Haveriberedskap och hög tillgänglighet för Azure-program][dr-ha-guide]
- Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
