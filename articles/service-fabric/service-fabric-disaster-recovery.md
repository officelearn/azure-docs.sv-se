---
title: Azure Service Fabric-katastrofåterställning | Microsoft Docs
description: Azure Service Fabric erbjuder funktionerna som är nödvändiga för att hantera alla typer av katastrofer. Den här artikeln beskrivs vilka typer av katastrofer som kan uppstå och hur du hanterar dem.
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
ms.openlocfilehash: 295772b70529f79c7a4c135d8ea7c12a1c661fe6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Katastrofåterställning i Azure Service Fabric
En viktig del av ger hög tillgänglighet är att säkerställa att tjänster kan överleva alla olika typer av fel. Detta är särskilt viktigt för fel som är oplanerade och utanför din kontroll. Den här artikeln beskriver vissa vanliga feltillstånd som kan vara katastrofer om inte modelleras och hanteras korrekt. Det beskrivs åtgärder och åtgärder som ska vidtas om en katastrof hände ändå. Målet är att begränsa eller att inga avbrott eller dataförlust när de uppstår fel, planerade eller annars kan uppstå.

## <a name="avoiding-disaster"></a>Undvika katastrofåterställning
Service Fabric primära målet är att hjälpa dig att modellen både din miljö och dina tjänster så att den vanliga fel typer inte är katastrofer. 

I allmänhet finns två typer av disaster/scenarier:

1. Maskinvaru- eller fel
2. Använd fel

### <a name="hardware-and-software-faults"></a>Fel för maskinvara och programvara
Fel för maskinvara och programvara är oförutsägbart. Det enklaste sättet att överleva fel körs fler kopior av tjänsten omfattas över maskinvaru- eller fault-gränser. Om din tjänst körs bara på en viss dator, är fel på att en dator en katastrofåterställning för tjänsten. Det enkla sättet att undvika den här katastrofåterställning är att se till att tjänsten faktiskt körs på flera datorer. Testning är också nödvändigt för att säkerställa fel på en dator inte störa tjänsten körs. Kapacitetsplanering säkerställer en ersättning instans kan skapas på en annan plats och att minskad kapacitet inte överlagra kvarvarande tjänster. Samma mönster fungerar oavsett vad du vill undvika fel i. Till exempel. Om du vill göra om ett fel i ett SAN-nätverk, köra över flera SAN-nätverk. Om du vill göra om förlust av en samling servrar, kör över flera rack. Om du oroar förlust av datacenter, ska tjänsten köras i flera Azure-regioner eller Datacenter. 

När den körs i den här typen av disklänkande läge du är fortfarande gäller vissa typer av samtidiga fel, men en och även flera fel i en viss typ (ex: en enskild virtuell dator eller nätverket länken misslyckas) hanteras automatiskt (och därför inte längre en ”katastrof”). Service Fabric innehåller många metoder för att expandera klustret och hanterar ta misslyckades noder och tjänster tillbaka. Service Fabric kan också köra flera instanser av dina tjänster för att undvika dessa typer av oplanerade fel från att omvandla till riktiga katastrofer.

Det kan finnas skäl varför kör en distribution som är tillräckligt stor för att omfatta över fel inte är möjligt. Det kan till exempel ta mer maskinvaruresurser än du är beredd att betala för i förhållande till risken för fel. När du hanterar distribuerade program, kan det bero på att ytterligare kommunikation hopp eller tillstånd replikering kostnader över geografiska avstånd orsaker acceptabel svarstid. Om den här raden ritas skiljer sig för varje program. För programvarufel specifikt kan felet vara i den tjänst som du försöker skala. I det här fallet förhindra inte fler kopior katastrofåterställning, eftersom feltillståndet korreleras över alla instanser.

### <a name="operational-faults"></a>Använd fel
Även om tjänsten är sträcker sig över hela världen med många uppsägningar, kan det fortfarande har katastrofala händelser. Till exempel om någon av misstag konfigurerar dns-namn för tjänsten eller tar bort den direkt. Exempel anta att du har en tillståndskänslig Service Fabric-tjänst och någon av misstag tas bort tjänsten. Om det inte finns några andra minskning, tjänsten och alla tillstånd den hade är nu rest. Dessa typer av operativa katastrofer kräver (”hoppsan”) olika åtgärder och steg för återställning än vanliga oplanerade fel. 

Det bästa sättet att undvika den här typen av operativa fel är att
1. begränsa operativa åtkomst till miljön
2. strikt granskningsåtgärder farliga
3. införa automation, förhindra manuell eller out-of-band-ändringar och verifiera specifika ändringar mot de faktiska miljön innan anta dem
4. Se till att skadliga åtgärder ”soft”. Mjuka operations börjar inte gälla omedelbart eller kan ångras i vissa tidsfönstret

Service Fabric innehåller vissa mekanismer för att förhindra operativa fel, till exempel att tillhandahålla [rollbaserad](service-fabric-cluster-security-roles.md) åtkomstkontroll för klusteråtgärder för. De flesta av dessa operativa fel kräver dock arbete och andra system. Service Fabric tillhandahåller en mekanism för kvarvarande operativa fel, framför allt säkerhetskopiering och återställning för tillståndskänsliga tjänster.

## <a name="managing-failures"></a>Hantera fel
Syftet med Service Fabric är nästan alltid automatisk hantering av fel. Tjänsterna måste dock för att hantera vissa typer av fel har ytterligare kod. Andra typer av fel bör _inte_ åtgärdas automatiskt av säkerhet och business continuity skäl. 

### <a name="handling-single-failures"></a>Enkel hantering-fel
Enskild datorerna kan växlas till en mängd olika skäl. Vissa av dessa är maskinvara orsaker som strömkällor och nätverk maskinvarufel. Andra fel finns i programvaran. Dessa inkluderar fel i själva operativsystemet och själva tjänsten. Service Fabric identifierar automatiskt dessa typer av fel, inklusive fall där datorn blir isolerad från andra datorer på grund av nätverksproblem.

Oavsett vilken typ av tjänsten kör en enda instans resultat driftstopp för tjänsten om den enda kopian av koden misslyckas av någon anledning. 

För att hantera varje enskilt fel är det enklaste som du kan göra att säkerställa att dina tjänster körs på fler än en nod som standard. För tillståndslösa tjänster detta kan åstadkommas genom att låta en `InstanceCount` större än 1. För tillståndskänsliga tjänster minsta rekommendationen är alltid en `TargetReplicaSetSize` och `MinReplicaSetSize` på minst 3. Kör fler kopior av koden för tjänsten säkerställer att din tjänst kan hantera varje enskilt fel automatiskt. 

### <a name="handling-coordinated-failures"></a>Hantering av koordineras fel
Samordnade fel kan inträffa i ett kluster på grund av något planerad eller oplanerad infrastruktur fel och ändringar eller planerade programändringar. Service Fabric modeller infrastruktur zoner som samordnade fel som Feldomäner uppstår. Områden som får samordnade programvaruändringar modelleras som uppgradera domäner. Mer information om fel- och domäner finns i [dokumentet](service-fabric-cluster-resource-manager-cluster-description.md) som beskriver klustertopologi och definition.

Som standard anser Service Fabric-fel och uppgradera domäner när du planerar där dina tjänster ska köras. Som standard försöker Service Fabric se till att dina tjänster körs över flera fel- och domäner så om planerad eller oplanerad ändringar sker tjänsterna är tillgängliga. 

Anta exempelvis att fel i ett eluttag gör att en samling datorer misslyckas samtidigt. Blir ett exempel på en enda misslyckades för en viss tjänst med flera kopior av tjänsten körs förlust av många datorer i fel domän fel. Det är därför hantera feldomäner är viktiga för att garantera hög tillgänglighet för dina tjänster. När du kör Service Fabric i Azure, hanteras automatiskt feldomäner. I andra miljöer kanske de inte. Om du utvecklar dina egna kluster lokalt, måste du mappa och planera fel domän layouten på rätt sätt.

Uppgraderingsdomäner är användbara för att modellera områden där programmet kommer att uppgraderas samtidigt. Därför definiera uppgradera domäner ofta även där programvara tas under planerade uppgraderingar. Uppgraderingar av både Service Fabric och dina tjänster Följ samma modell. Mer information om rullande uppgraderingar och uppgraderingsdomäner health Service Fabric-modellen som förhindrar oväntade ändringar påverkar klustret och din tjänst finns i dessa dokument:

 - [Uppgradering av programmet](service-fabric-application-upgrade.md)
 - [Uppgradera självstudien](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric-Hälsomodell](service-fabric-health-introduction.md)

Du kan visualisera layouten för klustret med hjälp av listan över lediga kluster som anges i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![Noder som är fördelade på feldomäner i Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modeling områden för fel, rullande uppgraderingar, kör flera instanser av Tjänstkod och tillstånd, placeringsregler så att dina tjänster köra fel- och domäner och inbyggda hälsoövervakning är bara **vissa** funktioner som Service Fabric tillhandahåller för att förhindra att omvandla till katastrofer normala operativa problem och fel. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Hantering av samtidiga maskinvaru- eller fel
Högre diskuterats vi enstaka fel. Som du ser är lätta att hantera för både tillståndslösa och tillståndskänsliga tjänster genom att hålla fler kopior av koden (och tillstånd) körs i fel- och uppgraderingsdomäner. Flera samtidiga slumpmässiga fel kan också inträffa. Dessa är mer sannolikt att leda till en verklig katastrof.


### <a name="random-failures-leading-to-service-failures"></a>Slumpmässiga fel som leder till tjänstfel inträffar
Anta att tjänsten hade en `InstanceCount` 5 och flera noder kör dessa alla instanser misslyckades på samma gång. Service Fabric svarar genom att automatiskt skapa ersättning instanser på andra noder. Kommer att fortsätta skapa ersättningar förrän tjänsten är tillbaka till den önskade instanser. Ett annat exempel anta att det fanns en tillståndslös tjänst med en `InstanceCount`1, vilket innebär att den körs på alla noder i klustret som giltig. Anta att vissa av dessa instanser skulle sluta fungera. I det här fallet meddelanden Service Fabric att tjänsten inte är dess status som önskas, och försöker skapa instanser på noderna där de saknas. 

För tillståndskänsliga tjänster beror situationen på om tjänsten har sparat tillstånd eller inte. Det beror också på hur många repliker tjänsten har och hur många misslyckades. Avgöra om en katastrof uppstod för en tillståndskänslig service och hanteringen av den följer tre steg:

1. Bestämma om det har förekommit förlorar kvorum eller inte
 - En förlorar kvorum är när en majoritet av replikerna för en tillståndskänslig service är nere samtidigt, inklusive den primära servern.
2. Bestämma om förlorar kvorum är permanent
 - I de flesta fall, är fel tillfälligt. Processer har startats om, noder har startats om, virtuella datorer är relaunched, läka nätverkspartitioner. Ibland om fel är permanent. 
    - För tjänster utan beständiga tillståndet kan ett fel i minst ett kvorum av repliker resultat _omedelbart_ förlorar kvorum permanent. När Service Fabric upptäcker förlorar kvorum i en tillståndskänslig icke-beständig tjänst, fortsätter omedelbart den till steg 3 genom att deklarera (eventuella) dataloss. Du kan fortsätta att dataloss är meningsfullt eftersom Service Fabric vet att det inte finns någon punkt i väntan på att replikerna komma tillbaka, eftersom även om de har återställts de skulle vara tom.
    - För tillståndskänsliga beständiga tjänster orsakar ett fel i minst ett kvorum av repliker Service Fabric att starta väntar på att gå tillbaka och återställa kvorum replikerna. Detta resulterar i ett avbrott för alla _skriver_ berörda partition (eller ”replikuppsättningen”) för tjänsten. Läser kan dock fortfarande vara möjligt med nedsatt konsekvens garanterar. Standardmängden tid som Service Fabric väntar kvorum som ska återställas är oändligt, eftersom du fortsätter är en (eventuella) dataloss händelse och har andra risker. Åsidosätta standardvärdet `QuorumLossWaitDuration` värdet går men rekommenderas inte. För tillfället bör i stället alla ansträngningar göras att återställa på replikerna. Detta kräver att noder som är nere säkerhetskopiera och att säkerställa att de kan återansluta enheter där de lagras lokalt beständiga tillståndet. Om du förlorar kvorum orsakas av fel i försöker Service Fabric automatiskt återskapa processer och starta om repliker i dem. Om detta misslyckas rapporterar Service Fabric hälsa fel. Om de kan lösas kommer replikerna vanligtvis tillbaka. Ibland kan dock kan replikerna återtas. Till exempel enheterna som alla har misslyckats eller datorerna förstörs fysiskt på något sätt. I dessa fall har vi nu en permanent kvorum dataförlust inträffat. Om du vill ge Service Fabric att stoppa väntar på att nedåt replikerna komma tillbaka en Klusteradministratör måste ta reda på vilka partitioner av vilka tjänster som påverkas och anropa den `Repair-ServiceFabricPartition -PartitionId` eller ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API.  Detta API kan du ange ID för partitionen som flytta utanför QuorumLoss till potentiella dataloss.

> [!NOTE]
> Det är _aldrig_ säkert att använda detta API än på ett sätt som är riktad mot specifika partitioner. 
>

3. Avgöra om det har förekommit faktiska data går förlorade och återställning från säkerhetskopior
  - När Service Fabric anropar den `OnDataLossAsync` metod är det alltid eftersom _misstänkt_ dataloss. Service Fabric garanterar att anropet levereras till den _bästa_ återstående replik. Det här är repliken har gjort i de flesta pågår. Orsaken alltid innebär _misstänkt_ dataloss är att det är möjligt att återstående repliken faktiskt har alla samma tillstånd som den primära servern när den avslutades. Men utan att det ska jämföras med den statusen finns det inget bra sätt för Service Fabric eller ansvariga för att ta reda på. Service Fabric vet nu även andra replikerna inte kommer tillbaka. Som var den beslut om när vi stoppats och väntar förlorar kvorum att lösa sig själv. Bästa loppet av åtgärd för tjänsten är vanligtvis att låsa och vänta tills särskilda administrativa åtgärder. Därför det gör att en typisk implementering av den `OnDataLossAsync` metoden göra?
  - Logga först som `OnDataLossAsync` har utlösts och utlösa alla nödvändiga administrativa varningar.
   - Vanligtvis på den här punkten, pausa och vänta på ytterligare beslut och manuella åtgärder som ska vidtas. Det beror på att även om säkerhetskopior är tillgängliga kan de måste förberedas. Till exempel om två olika tjänster samordna information kan säkerhetskopieringarna behöva ändras för att säkerställa att när återställningen sker som informationen om dessa två tjänster försiktighet om konsekvent. 
  - Ofta finns även vissa andra telemetri eller avgaser från tjänsten. Dessa metadata kan finnas i loggarna eller i andra tjänster. Den här informationen kan användas för att identifiera om det fanns någon anrop emot och bearbetas på primärt som fanns inte i säkerhetskopian eller replikeras till viss replikeringen. Dessa kan behöva spelas eller lagts till i säkerhetskopian innan det är möjligt att återställningen.  
   - Jämförelser av återstående repliken tillstånd som alla säkerhetskopior som är tillgängliga. Om du använder Service Fabric tillförlitliga samlingar finns det verktyg och bearbetar tillgängliga för att göra det, beskrivs i [i den här artikeln](service-fabric-reliable-services-backup-restore.md). Målet är att se om tillståndet inom repliken räcker eller också vilka säkerhetskopieringen kanske saknas.
  - När jämförelsen görs och behövs för återställningen slutfördes, kod som ska returnera true om alla tillstånd har ändrats. Om repliken har fastställt att den var den bästa tillgängliga kopian av status och gjort några ändringar, returnera false. SANT anger att de _andra_ återstående repliker kan nu inte stämmer överens med den här. De tas bort och återskapas från den här repliken. Falskt anger att inga tillståndsändringar har gjorts, så att de andra replikerna kan hålla de har. 

Det är ytterst viktigt att tjänsten författare öva potentiella dataloss och scenarier innan tjänster aldrig har distribuerats i produktionsmiljön. För att skydda mot risken för dataloss, är det viktigt att regelbundet [säkerhetskopiera tillståndet](service-fabric-reliable-services-backup-restore.md) för någon av dina tillståndskänsliga tjänster till en geo-redundant lagring. Du måste också kontrollera att du har möjlighet att återställa den. Eftersom säkerhetskopior av många olika tjänster utförs vid olika tidpunkter, måste du se till att dina tjänster har en konsekvent överblick över varandra efter en återställning. Anta till exempel att en situation där en tjänst genererar ett tal och lagrar det och skickar den till en annan tjänst som lagrar även den. Efter en återställning kan du märka att andra tjänsten har numret men första finns inte, eftersom den är säkerhetskopiering inte angav åtgärden.

Om du upptäcker att återstående repliker är tillräckligt för att fortsätta från i ett scenario med dataloss och du kan inte återskapa Tjänststatus från telemetri eller avgaser, anger frekvensen för dina säkerhetskopieringar din bästa möjliga återställningspunktmål (RPO). Service Fabric innehåller många verktyg för att testa olika scenarier, inklusive permanent kvorum och dataloss som kräver återställning från en säkerhetskopia. Dessa scenarier ingår som en del av Service Fabric datatillgång verktyg, hanteras av fel Analysis Service. Mer information om dessa verktyg och mönster finns [här](service-fabric-testability-overview.md). 

> [!NOTE]
> Systemtjänster kan också påverkas kvorum försvinner, med påverkan på tjänsten i fråga. Till exempel påverkar kvorumförlust i naming service namnmatchning, medan förlorar kvorum i failover manager service blockerar skapandet av ny tjänst och växling vid fel. När Service Fabric-systemtjänster följer samma mönster som dina tjänster för tillståndshantering av, rekommenderas inte att bör du försöka flyttas utanför förlorar kvorum och till eventuella dataloss. Det rekommenderas att i stället till [begära support](service-fabric-support.md) att fastställa en lösning som är riktad mot dina behov.  Vanligtvis är det bättre att vänta tills nedåt replikerna returnera.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Tillgängligheten för Service Fabric-kluster
Generellt sett är själva Service Fabric-klustret en miljö med några enskilda felpunkter. Ett fel på någon en nod inte ska orsaka tillgänglighet eller tillförlitlighetsproblem för klustret, främst eftersom Service Fabric-systemtjänster Följ samma anvisningar som tillhandahölls tidigare: de alltid körs med tre eller flera repliker som standard och de systemet tjänster som är tillståndslös köras på alla noder. De underliggande Service Fabric nätverk och misslyckade identifiering lagren distribueras helt. De flesta systemtjänster kan byggas från metadata i klustret eller vet hur man synkroniserar du om deras tillstånd från andra platser. Tillgängligheten för klustret kan vara hotad om systemtjänster får i kvorum förlust situationer som de som beskrivs ovan. I dessa fall kan du inte att kunna utföra vissa åtgärder på klustret som påbörjar en uppgradering eller distribuera nya tjänster, men själva klustret är fortfarande in. Tjänster på redan körs fortsätter att köras i dessa villkor om de inte behöver skrivningar till systemtjänster ska fortsätta att fungera. Till exempel om Failover Manager är förlorar kvorum alla tjänster fortsätter att köras, men alla tjänster som inte kommer inte kunna startas om automatiskt, eftersom det kräver medverkan av Failover Manager. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Fel i en datacenter- eller Azure-region
I sällsynta fall kan blir en fysiska Datacenter tillfälligt otillgänglig på grund av förlust av power eller nätverksanslutning. I dessa fall måste blir din Service Fabric-kluster och tjänster i datacenter eller Azure-region otillgänglig. Dock _dina data bevaras_. För kluster som körs i Azure, kan du visa uppdateringar på avbrott på den [status för Azure][azure-status-dashboard]. Hög förmodan att fysiska Datacenter helt eller delvis förstörs alla Service Fabric-kluster som värd för det eller tjänsterna i dem kan gå förlorade. Detta inkluderar några tillstånd som inte säkerhetskopieras utanför det datacenter eller regionen.

Det finns två olika strategier för kvarvarande permanent eller varaktigt fel på ett enda datacenter eller en region. 

1. Kör separata Service Fabric-kluster i flera områden, och använda någon mekanism för växling vid fel och redundansväxla mellan dessa miljöer. Den här typen av flera klustermodellen för aktiv-aktiv eller aktivt-passivt kräver ytterligare kod för hantering och åtgärder. Detta kräver samordning av säkerhetskopior från tjänster i ett datacenter eller region så att de är tillgängliga i andra Datacenter när en misslyckas. 
2. Kör ett enda Service Fabric-kluster som omfattar flera Datacenter eller regioner. Det minsta konfigurationen som stöds för det här är tre Datacenter eller regioner. Det rekommenderade antalet regioner eller Datacenter är fem. Detta kräver en mer komplex klustertopologi. Fördelen med den här modellen är dock att fel i ett datacenter eller region konverteras till ett vanligt fel från en katastrof. Dessa fel kan hanteras av de metoder som fungerar för kluster inom en enskild region. Se till arbetsbelastningar distribueras så att de tolerera normal fel feldomäner, uppgraderingsdomäner och regler för Service Fabric-placering. Mer information om principer som kan hjälpa dig att använda tjänster i den här typen av klustret läsa på [placeringsprinciper](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Slumpmässiga fel som leder till klusterfel
Service Fabric har begreppet Seed-noder. Dessa är noder som underhåller tillgängligheten för underliggande klustret. Dessa noder att se till att klustret förblir upp genom att upprätta lån med andra noder och fungerar som tiebreakers under vissa typer av nätverksfel. Om slumpmässiga fel tar bort en majoritet av seed-noder i klustret och de inte återförts, stängs klustret automatiskt. I Azure, hanteras automatiskt Seed noder: de distribueras över tillgängliga fel- och uppgraderingsdomäner och om en enda seed-nod har tagits bort från klustret skapas en annan i dess ställe. 

I både fristående Service Fabric-kluster och Azure är ”primära nodtypen” den som kör frö. När du definierar en primära nodtypen Service Fabric automatiskt att dra nytta av antalet noder som tillhandahålls genom att skapa upp till 9 seed-noder och 9 repliker av var och en av systemtjänsterna. Om en uppsättning slumpmässiga fel tar ut en majoritet av dessa system service repliker samtidigt ange systemtjänsterna kvorum försvinner, som det beskrivs ovan. Om en majoritet av seed-noder går förlorade, stängs klustret strax efter.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du simulera olika fel med den [datatillgång framework](service-fabric-testability-overview.md)
- Läs andra resurser för katastrofåterställning och hög tillgänglighet. Microsoft har publicerat en stor mängd information i dessa avsnitt. Även om vissa dokument refererar till specifika tekniker för användning i andra produkter, innehåller många allmänna metodtips som du kan använda i kontexten Service Fabric:
  - [Tillgänglighetschecklista](../best-practices-availability-checklist.md)
  - [Utför en katastrofåterställning återställningsgranskning](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Katastrofåterställning och hög tillgänglighet för Azure-program][dr-ha-guide]
- Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
