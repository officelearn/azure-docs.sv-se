---
title: Referensarkitekturer för Oracle-databaser på Azure | Microsoft-dokument
description: Refererar till arkitekturer för att köra Oracle Database Enterprise Edition-databaser på Microsoft Azure Virtual Machines.
services: virtual-machines-linux
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 235482f5d44877e5c4e47aed4d7eaf2baea5c3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75560342"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Referensarkitekturer för Oracle Database Enterprise Edition på Azure

Den här guiden innehåller information om hur du distribuerar en Oracle-databas med hög tillgänglighet på Azure. Dessutom dyker den här guiden in i överväganden om katastrofåterställning. Dessa arkitekturer har skapats baserat på kunddistributioner. Den här guiden gäller endast Oracle Database Enterprise Edition.

Om du är intresserad av att lära dig mer om hur du maximerar prestanda för oracle-databasen läser du [Skapa en Oracle DB.](oracle-design.md)

## <a name="assumptions"></a>Antaganden

- Du har en förståelse för de olika begreppen i Azure, till exempel [tillgänglighetszoner](../../../availability-zones/az-overview.md)
- Du kör Oracle Database Enterprise Edition 12c eller senare
- Du är medveten om och bekräftar licenskonsekvenserna när du använder lösningarna i den här artikeln

## <a name="high-availability-for-oracle-databases"></a>Hög tillgänglighet för Oracle-databaser

Att uppnå hög tillgänglighet i molnet är en viktig del av varje organisations planering och design. Microsoft Azure erbjuder [tillgänglighetszoner](../../../availability-zones/az-overview.md) och tillgänglighetsuppsättningar (som ska användas i regioner där tillgänglighetszoner inte är tillgängliga). Läs mer om [hur du hanterar tillgängligheten för dina virtuella datorer](../../../virtual-machines/linux/manage-availability.md) för att designa för molnet.

Förutom molnbaserade verktyg och erbjudanden tillhandahåller Oracle lösningar för hög tillgänglighet som [Oracle Data Guard,](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7) [Data Guard med FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)och [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) som kan konfigureras på Azure. Den här guiden täcker referensarkitekturer för var och en av dessa lösningar.

Slutligen, när du migrerar eller skapar program för molnet, är det viktigt att justera programkoden för att lägga till molninbyggda mönster som [återförsöksmönster](https://docs.microsoft.com/azure/architecture/patterns/retry) och [kretsbrytaremönster](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker). Ytterligare mönster som definieras i [guiden Molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns/) kan hjälpa ditt program att bli mer flexibelt.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC i molnet

Oracle Real Application Cluster (RAC) är en lösning från Oracle för att hjälpa kunderna att uppnå höga dataflöde genom att ha många instanser som får åtkomst till en databaslagring (arkitekturmönster för delad alla). Oracle RAC kan också användas för hög tillgänglighet lokalt, men Oracle RAC kan inte användas för hög tillgänglighet i molnet eftersom det bara skyddar mot instansnivåfel och inte mot fel på Rack-nivå eller datacenternivå. Därför rekommenderar Oracle att du använder Oracle Data Guard med databasen (oavsett om det är en instans eller RAC) för hög tillgänglighet. Kunder kräver i allmänhet ett högt serviceavtal för att köra sina verksamhetskritiska program. Oracle RAC är för närvarande inte certifierat eller stöds av Oracle på Azure. Azure erbjuder dock funktioner som Azure erbjuder tillgänglighetszoner och planerade underhållsfönster för att skydda mot instansnivåfel. Utöver detta kan kunder använda tekniker som Oracle Data Guard, Oracle GoldenGate och Oracle Sharding för hög prestanda och resiliancy genom att skydda sina databaser från racknivå samt datacenter-nivå och geopolitiska fel.

När du kör Oracle-databaser över flera [tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview) tillsammans med Oracle Data Guard eller GoldenGate kan kunderna få ett SLA på 99,99 %. I Azure-regioner där tillgänglighetszoner ännu inte finns kan kunder använda [tillgänglighetsuppsättningar](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) och uppnå ett SLA på 99,95 %.

>Du kan ha ett upptidsmål som är mycket högre än det SLA som tillhandahålls av Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Haveriberedskap för Oracle-databaser

När du är värd för dina verksamhetskritiska program i molnet är det viktigt att designa för hög tillgänglighet och haveriberedskap.

För Oracle Database Enterprise Edition är Oracle Data Guard en användbar funktion för haveriberedskap. Du kan ställa in en instans för väntedatabas i en [parkopplad Azure-region](/azure/best-practices-availability-paired-regions) och konfigurera databevakningsväxling för haveriberedskap. För noll dataförlust rekommenderar vi att du distribuerar en Oracle Data Guard Far Sync-instans utöver Active Data Guard. 

Överväg att konfigurera Data Guard Far Sync-instansen i en annan tillgänglighetszon än din Oracle-primära databas om ditt program tillåter svarstid (grundlig testning krävs). Använd ett läge **med maximal tillgänglighet** för att ställa in synkron transport av dina gör om-filer till Far Sync-instansen. Dessa filer överförs sedan asynkront till standby-databasen. 

Om ditt program inte tillåter prestandaförlust när du konfigurerar Far Sync-instans i en annan tillgänglighetszon i **läget Maximal tillgänglighet** (synkron), kan du ställa in en Far Sync-instans i samma tillgänglighetszon som din primära databas. För extra tillgänglighet bör du överväga att konfigurera flera Far Sync-instanser nära din primära databas och minst en instans nära väntedatabasen (om rollen övergår). Läs mer om Oracle Data Guard Far Sync i detta [Oracle Active Data Guard Far Sync whitepaper](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

När du använder Oracle Standard Edition-databaser finns det ISV-lösningar som DBVisit Standby som gör att du kan konfigurera hög tillgänglighet och haveriberedskap.

## <a name="reference-architectures"></a>Referensarkitekturer

### <a name="oracle-data-guard"></a>Oracle Data Guard

Oracle Data Guard säkerställer hög tillgänglighet, dataskydd och haveriberedskap för företagsdata. Data Guard underhåller standby-databaser som transaktionskonsekventa kopior av den primära databasen. Beroende på avståndet mellan de primära och sekundära databaserna och programtoleransen för svarstid kan du ställa in synkron eller asynkron replikering. Om den primära databasen inte är tillgänglig på grund av ett planerat eller oplanerat avbrott kan Data Guard växla alla väntedatabaser till den primära rollen, vilket minimerar driftstopp. 

När du använder Oracle Data Guard kan du också öppna din sekundära databas för skrivskyddade ändamål. Den här konfigurationen kallas Active Data Guard. Oracle Database 12c introducerade en funktion som kallas Data Guard Far Sync Instance. Med den här instansen kan du ställa in en konfiguration av nolldataförlust för Oracle-databasen utan att behöva kompromissa med prestanda.

> [!NOTE]
> Active Data Guard kräver ytterligare licensiering. Den här licensen krävs också för att använda Far Sync-funktionen. Kontakta din Oracle-representant för att diskutera licenskonsekvenserna.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard med FSFO
Oracle Data Guard med Snabbstart Redundans (FSFO) kan ge ytterligare återhämtning genom att ställa in mäklaren på en separat dator. Data Guard-mäklaren och den sekundära databasen både kör observatören och observerar den primära databasen för driftstopp. Detta möjliggör redundans i din Data Guard observatör setup också. 

Med Oracle Database version 12.2 och högre, är det också möjligt att konfigurera flera observatörer med en enda Oracle Data Guard mäklare konfiguration. Den här inställningen ger ytterligare tillgänglighet om en observatör och den sekundära databasen upplever driftstopp. Data Guard Broker är lätt och kan vara värd på en relativt liten virtuell maskin. Om du vill veta mer om Data Guard Broker och dess fördelar kan du besöka [Oracle-dokumentationen](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) om det här avsnittet.

Följande diagram är en rekommenderad arkitektur för att använda Oracle Data Guard på Azure med tillgänglighetszoner. Med den här arkitekturen kan du få ett SLA för drifttid för vm på 99,99 %.

![Oracle-databas med hjälp av tillgänglighetszoner med Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

I föregående diagram kommer klientsystemet åt ett anpassat program med Oracle-backend via webben. Webbfrontend är konfigurerad i en belastningsutjämnare. Webbfrontend ringer ett samtal till rätt programserver för att hantera arbetet. Programservern frågar den primära Oracle-databasen. Oracle-databasen har konfigurerats med hjälp av en hypertrådad [minnesoptimerad virtuell dator](../../../virtual-machines/windows/sizes-memory.md) med [begränsade centrala virtuella processorer](../../../virtual-machines/windows/constrained-vcpu.md) för att spara på licenskostnader och maximera prestanda. Flera premium- eller ultradiskar (hanterade diskar) används för prestanda och hög tillgänglighet.

Oracle-databaserna placeras i flera tillgänglighetszoner för hög tillgänglighet. Varje zon består av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning ställs minst tre separata zoner in i alla aktiverade regioner. Den fysiska separationen av tillgänglighetszoner inom en region skyddar data från datacenterfel. Dessutom ställs två FSFO-observatörer in över två tillgänglighetszoner för att initiera och växla över databasen till den sekundära när ett avbrott inträffar. 

Du kan ställa in ytterligare observatörer och/eller väntedatabaser i en annan tillgänglighetszon (AZ 1, i det här fallet) än den zon som visas i föregående arkitektur. Slutligen övervakas Oracle-databaser för drifttid och prestanda av Oracle Enterprise Manager (OEM). OEM kan du också generera olika prestanda- och användningsrapporter.

I regioner där tillgänglighetszoner inte stöds kan du använda tillgänglighetsuppsättningar för att distribuera din Oracle-databas på ett högtillgänglighetssätt. Med tillgänglighetsuppsättningar kan du uppnå en drifttid för virtuella datorer på 99,95 %. Följande diagram är en referensarkitektur för den här användningen:

![Oracle-databas med hjälp av tillgänglighetsuppsättningar med Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Den virtuella datorn oracle Enterprise Manager behöver inte placeras i en tillgänglighetsuppsättning eftersom det bara finns en instans av OEM-tillverkaren som distribueras.
> * Ultra diskar stöds för närvarande inte i en konfiguration av tillgänglighetsuppsättningar.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard långt synkroniserat

Oracle Data Guard Far Sync ger noll dataförlustskyddsfunktioner för Oracle-databaser. Med den här funktionen kan du skydda mot dataförlust i om databasdatorn misslyckas. Oracle Data Guard Far Sync måste installeras på en separat virtuell dator. Far Sync är en lätt Oracle-instans som bara har en kontrollfil, lösenordsfil, spfile och standby-loggar. Det finns inga datafiler eller rego loggfiler. 

För noll dataskydd för dataförlust måste det finnas synkron kommunikation mellan din primära databas och Far Sync-instansen. Far Sync-instansen tar emot gör om från den primära på ett synkront sätt och vidarebefordrar den omedelbart till alla standby-databaser på ett asynkront sätt. Den här inställningen minskar också belastningen på den primära databasen, eftersom den bara behöver skicka gör om till Far Sync-instansen i stället för alla standby-databaser. Om en Far Sync-instans misslyckas använder Data Guard automatiskt asynkron transport till den sekundära databasen från den primära databasen för att upprätthålla dataskydd med nära noll dataförlust. För extra återhämtning kan kunder distribuera flera Far Sync-instanser per varje databasinstans (primär och sekundär).

Följande diagram är en arkitektur med hög tillgänglighet med Oracle Data Guard Far Sync:

![Oracle-databas med hjälp av tillgänglighetszoner med Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

I föregående arkitektur finns det en Far Sync-instans som distribueras i samma tillgänglighetszon som databasinstansen för att minska svarstiden mellan de två. I de fall där programmet är latenskänsligt kan du överväga att distribuera databasen och Far Sync-instansen eller instanserna i en [närhetsplaceringsgrupp](../../../virtual-machines/linux/proximity-placement-groups.md).

Följande diagram är en arkitektur som använder Oracle Data Guard FSFO och Far Sync för att uppnå hög tillgänglighet och haveriberedskap:

![Oracle-databas med hjälp av tillgänglighetszoner för haveriberedskap med Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate möjliggör utbyte och manipulering av data på transaktionsnivå mellan flera, heterogena plattformar i hela företaget. Den flyttar genomförda transaktioner med transaktionsintegritet och minimal omkostnader på din befintliga infrastruktur. Dess modulära arkitektur ger dig flexibiliteten att extrahera och replikera valda dataposter, transaktionsändringar och ändringar av DDL (datadefinitionsspråk) över en mängd olika topologier.

Oracle GoldenGate kan du konfigurera din databas för hög tillgänglighet genom att tillhandahålla dubbelriktad replikering. På så sätt kan du konfigurera en konfiguration med **flera hanterare** eller **aktiva aktiva**. Följande diagram är en rekommenderad arkitektur för Oracle GoldenGate aktiv installation på Azure. I följande arkitektur har Oracle-databasen konfigurerats med hjälp av en hypertrådad [minnesoptimerad virtuell dator](../../../virtual-machines/windows/sizes-memory.md) med [begränsade centrala virtuella processorer](../../../virtual-machines/windows/constrained-vcpu.md) för att spara på licenskostnader och maximera prestanda. Flera premium- eller ultradiskar (hanterade diskar) används för prestanda och tillgänglighet.

![Oracle-databas med hjälp av tillgänglighetszoner med Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> En liknande arkitektur kan ställas in med hjälp av tillgänglighetsuppsättningar i regioner där tillgänglighetszoner för närvarande inte är tillgängliga.

Oracle GoldenGate har processer som Extrahera, Pump och Replicat som hjälper dig att asynkront replikera dina data från en Oracle-databasserver till en annan. Med dessa processer kan du ställa in en dubbelriktad replikering för att säkerställa hög tillgänglighet för databasen om det finns driftstopp på zonnivå. I föregående diagram körs extrahera-processen på samma server som Oracle-databasen, medan datapumpen och replikprocesserna körs på en separat server i samma tillgänglighetszon. Replikprocessen används för att ta emot data från databasen i den andra tillgänglighetszonen och arkivera data till Oracle-databasen i dess tillgänglighetszon. På samma sätt skickar datapumpsprocessen data som har extraherats genom extrahera processen till Replikprocessen i den andra tillgänglighetszonen. 

Medan det föregående arkitekturdiagrammet visar datapumpen och replikprocessen som konfigurerats på en separat server, kan du ställa in alla Oracle GoldenGate-processer på samma server, baserat på serverns kapacitet och användning. Konsultera alltid din AWR-rapport och måtten i Azure för att förstå användningsmönstret för din server.

När du konfigurerar Oracle GoldenGate dubbelriktad replikering i olika tillgänglighetszoner eller olika regioner är det viktigt att se till att svarstiden mellan de olika komponenterna är godtagbar för ditt program. Svarstiden mellan tillgänglighetszoner och regioner kan variera och beror på flera faktorer. Vi rekommenderar att du ställer in prestandatester mellan programnivån och databasnivån i olika tillgänglighetszoner och/eller regioner för att bekräfta att den uppfyller kraven för programmets prestanda.

Programnivån kan ställas in i ett eget undernät och databasnivån kan delas upp i sitt eget undernät. När det är möjligt bör du överväga att använda [Azure Application Gateway](../../../application-gateway/overview.md) för att ladda trafik mellan dina programservrar. Azure Application Gateway är en robust belastningsutjämnare för webbtrafik. Det ger cookie-baserad sessionstillhörighet som håller en användarsession på samma server, vilket minimerar konflikterna i databasen. Alternativ till Application Gateway är [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) och [Azure Traffic Manager](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Oracle Sharding

Sharding är ett datanivåmönster som introducerades i Oracle 12.2. Det gör att du kan horisontellt partitionera och skala dina data över oberoende databaser. Det är en share-nothing-arkitektur där varje databas finns på en dedikerad virtuell dator, vilket möjliggör hög läs- och skrivdataflöde utöver återhämtning och ökad tillgänglighet. Det här mönstret eliminerar enskilda felpunkter, ger felisolering och möjliggör rullande uppgraderingar utan driftstopp. Driftstoppet för en shard eller ett datacenternivåfel påverkar inte prestanda eller tillgänglighet för andra shards i andra datacenter. 

Sharding är lämplig för OLTP-program med högt dataflöde som inte har råd med några driftstopp. Alla rader med samma sharding-nyckel är alltid garanterade att vara på samma fragment, vilket ökar prestanda som ger hög konsekvens. Program som använder sharding måste ha en väldefinierad datamodell och datadistributionsstrategi (konsekvent hash, intervall, lista eller sammansatt) som i första hand kommer åt data med hjälp av en sharding-nyckel (till exempel *customerId* eller *accountNum*). Sharding kan du också lagra vissa uppsättningar data närmare slutkunderna, vilket hjälper dig att uppfylla dina prestanda- och efterlevnadskrav.

Vi rekommenderar att du replikerar dina shards för hög tillgänglighet och haveriberedskap. Den här installationen kan göras med Oracle-teknik som Oracle Data Guard eller Oracle GoldenGate. En replikeringsenhet kan vara en fragment, en del av en shard eller en grupp shards. Tillgängligheten för en fragmenterad databas påverkas inte av ett avbrott eller en avmattning av en eller flera shards. För hög tillgänglighet kan standby-shards placeras i samma tillgänglighetszon där de primära shardsna placeras. För haveriberedskap kan standby-shards finnas i en annan region. Du kan också distribuera shards i flera regioner för att betjäna trafiken i dessa regioner. Läs mer om hur du konfigurerar hög tillgänglighet och replikering av din fragmenterade databas i [Oracle Sharding-dokumentationen](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Oracle Sharding består huvudsakligen av följande komponenter. Mer information om dessa komponenter finns i [Oracle Sharding-dokumentationen:](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)

- **Shard-katalog** - Oracle-databas med särskilt syfte som är ett beständigt arkiv för alla Shard-databaskonfigurationsdata. Alla konfigurationsändringar, till exempel lägga till eller ta bort shards, mappning av data och DDLs i en fragmentdatabas initieras i fragmentkatalogen. Fragmentkatalogen innehåller också huvudkopian av alla duplicerade tabeller i en SDB. 

  Fragmentkatalogen använder materialiserade vyer för att automatiskt replikera ändringar i dubbletttabeller i alla shards. Fragmentkatalogdatabasen fungerar också som en frågekoordinator som används för att bearbeta flershardfrågor och frågor som inte anger en sharding-nyckel. 
  
  Att använda Oracle Data Guard tillsammans med tillgänglighetszoner eller tillgänglighetsuppsättningar för fragmentkatalogens höga tillgänglighet är en rekommenderad metod. Tillgängligheten för fragmentkatalogen påverkar inte tillgängligheten för den fragmenterade databasen. Ett driftstopp i fragmentkatalogen påverkar bara underhållsåtgärder och multishardfrågor under den korta period som databevakningsväxlingen slutför. Onlinetransaktioner fortsätter att dirigeras och utföras av SDB och påverkas inte av ett katalogutbrott.

- **Shard directors** – Lätta tjänster som måste distribueras i varje region/tillgänglighetszon som dina shards finns i. Shard Directors är global service managers som distribueras i samband med Oracle Sharding. För hög tillgänglighet rekommenderar vi att du distribuerar minst en fragmentregissör i varje tillgänglighetszon som dina shards finns i. 

  När du ansluter till databasen från början ställs routningsinformationen in av en fragmentregissör och cachelagras för efterföljande begäranden och kringgår fragmentdirektören. När sessionen har upprättats med en fragment, stöds alla SQL-frågor och DMLs i omfattningen av den angivna fragmentet. Den här routningen är snabb och används för alla OLTP-arbetsbelastningar som utför fragmenttransaktioner inom fragment. Vi rekommenderar att du använder direkt routning för alla OLTP-arbetsbelastningar som kräver högsta prestanda och tillgänglighet. Routningscachen uppdateras automatiskt när en shard blir otillgänglig eller ändringar sker i fragmenttopologin. 
  
  För databeroende routning med hög prestanda rekommenderar Oracle att du använder en anslutningspool när du öppnar data i den fragmenterade databasen. Oracle-anslutningspooler, språkspecifika bibliotek och drivrutiner stöder Oracle Sharding. Mer information finns i Dokumentationen till [Oracle Sharding.](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9)

- **Global service** - Global service liknar den vanliga databastjänsten. Förutom alla egenskaper för en databastjänst har en global tjänst egenskaper för fragmenterade databaser som regiontillhörighet mellan klienter och fragment- och replikeringsfördröjningstolerans. Endast en global tjänst behöver skapas för att läsa/skriva data till/från en fragmenterad databas. När du använder Active Data Guard och konfigurerar skrivskyddade repliker av shards kan du skapa en annan gGobal-tjänst för skrivskyddade arbetsbelastningar. Klienten kan använda dessa globala tjänster för att ansluta till databasen.

- **Fragmentdatabaser** - Shard-databaser är dina Oracle-databaser. Varje databas replikeras med Oracle Data Guard i en Broker-konfiguration med FSFO (Fast-Start Redundans) aktiverat. Du behöver inte ställa in databevakningsväxling och replikering på varje shard. Detta konfigureras och distribueras automatiskt när den delade databasen skapas. Om en viss shard misslyckas misslyckas Oracle-delning automatiskt över databasanslutningar från den primära till vänteläge.

Du kan distribuera och hantera Oracle-fragmenterade databaser med två gränssnitt: Oracle `GDSCTL` Enterprise Manager Cloud Control GUI och/eller kommandoradsverktyget. Du kan även övervaka de olika shards för tillgänglighet och prestanda med hjälp av cloud control. Kommandot `GDSCTL DEPLOY` skapar automatiskt shards och deras respektive lyssnare. Dessutom distribuerar det här kommandot automatiskt replikeringskonfigurationen som används för hög tillgänglighet på fragmentnivå som anges av administratören.

Det finns olika sätt att skapa en databas:

* Systemhanterad fragmentering – Distribuerar automatiskt över shards med partitionering
* Användardefinierad fragmentering - Gör att du kan ange mappning av data till shards, som fungerar bra när det finns reglerings- eller datalokaliseringskrav)
* Sammansatt fragmentering - En kombination av systemhanterad och användardefinierad fragmentering för olika _fragmentområden_
* Tabellunderpartitioner - Liknar en vanlig partitionerad tabell.

Läs mer om de olika [sharding metoderna](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) i Oracles dokumentation.

En fragmenterad databas kan se ut som en enda databas till program och utvecklare, men när du migrerar från en icke-fragmenterad databas till en fragmenterad databas krävs noggrann planering för att avgöra vilka tabeller som ska dupliceras jämfört med fragmenterade. 

Duplicerade tabeller lagras på alla shards, medan fragmenterade tabeller fördelas över olika shards. Rekommendationen är att duplicera små och dimensionella tabeller och distribuera/fragmentera faktatabellerna. Data kan läsas in i din fragmenterade databas med hjälp av antingen fragmentkatalogen som centralkoordinator eller genom att köra Datapump på varje shard. Läs mer om [att migrera data till en fragmenterad databas](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) i Oracles dokumentation.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle Sharding med Data Guard

Oracle Data Guard kan användas för sharding med systemhanterade, användardefinierade och sammansatta sharding-metoder.

Följande diagram är en referensarkitektur för Oracle Sharding med Oracle Data Guard som används för hög tillgänglighet för varje shard. Arkitekturdiagrammet visar en _sammansatt sharding-metod_. Arkitekturdiagrammet skiljer sig troligen åt för program med olika krav på datalokalitet, belastningsutjämning, hög tillgänglighet, haveriberedskap osv. Oracle Sharding kan du uppfylla dessa krav och skala horisontellt och effektivt genom att tillhandahålla dessa alternativ. En liknande arkitektur kan även distribueras med Oracle GoldenGate.

![Oracle Database Sharding med hjälp av tillgänglighetszoner med Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Systemhanterad sharding är det enklaste att konfigurera och hantera, men användardefinierad fragmentering eller sammansatt fragmentering är väl lämpad för scenarier där dina data och program är geodistribuerade eller i scenarier där du måste ha kontroll över replikeringen av varje skärva. 

I föregående arkitektur används sammansatt fragmentering för att geodistribuera data och vågrätt skala ut dina programnivåer. Sammansatt sharding är en kombination av systemhanterad och användardefinierad fragmentering och ger därmed fördelen av båda metoderna. I föregående scenario fragmenteras data först över flera fragmentområden åtskilda efter region. Sedan partitioneras data ytterligare genom konsekvent hash över flera shards i fragmentutrymmet. Varje fragmentutrymme innehåller flera shardgrupper. Varje fragmentgrupp har flera shards och är en "enhet" av replikering, i det här fallet. Varje fragmentgrupp innehåller alla data i fragmentutrymmet. Shardgroups A1 och B1 är primära shardgrupper, medan fragmentgrupperna A2 och B2 är väntelägen. Du kan välja att ha enskilda shards som replikeringsenhet i stället för en fragmentgrupp.

I föregående arkitektur distribueras en GSM/shard-chef i alla tillgänglighetszoner för hög tillgänglighet. Rekommendationen är att distribuera minst en GSM/shard-chef per datacenter/region. Dessutom distribueras en instans av programservern i alla tillgänglighetszoner som innehåller en fragmentgrupp. Med den här inställningen kan programmet hålla svarstiden mellan programservern och databasen/fragmentgruppen låg. Om en databas misslyckas kan programservern i samma zon som väntedatabasen hantera begäranden när databasrollövergången sker. Azure Application Gateway och shard-chefen håller reda på begäran och svarssvarstid och vägbegäranden därefter.

Från ett programperspektiv gör klientsystemet en begäran till Azure Application Gateway (eller andra belastningsutjämningstekniker i Azure) som omdirigerar begäran till den region som är närmast klienten. Azure Application Gateway stöder också klibbiga sessioner, så alla begäranden som kommer från samma klient dirigeras till samma programserver. Programservern använder anslutningspoolning i dataåtkomstdrivrutiner. Den här funktionen är tillgänglig i drivrutiner som JDBC, ODP.NET, OCI, etc. Drivrutinerna kan känna igen sharding nycklar som anges som en del av begäran. [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) för JDBC-klienter kan göra det möjligt för icke-Oracle-programklienter som Apache Tomcat och IIS att arbeta med Oracle Sharding. 

Under den första begäran ansluter programservern till fragmenthanteraren i regionen för att hämta routningsinformation för shard som begäran måste dirigeras till. Baserat på den sharding-nyckel som skickades dirigerar direktören programservern till respektive shard. Programservern cachelagrar den här informationen genom att skapa en karta och för efterföljande begäranden kringgår fragmentregissören och routsbegäranden direkt till shard.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle Sharding med GoldenGate

Följande diagram är en referensarkitektur för Oracle Sharding med Oracle GoldenGate för hög tillgänglighet för varje shard i regionen. I motsats till föregående arkitektur visar den här arkitekturen endast hög tillgänglighet inom en enda Azure-region (flera tillgänglighetszoner). Man kan distribuera en sharded databas med hög tillgänglighet med flera regioner (liknande föregående exempel) med Oracle GoldenGate.

![Oracle Database Sharding med hjälp av tillgänglighetszoner med GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

Den föregående referensarkitekturen använder _den systemhanterade sharding-metoden_ för att fragmentera data. Eftersom Oracle GoldenGate-replikering görs på en segmentnivå kan hälften av data som replikeras till en shard replikeras till en annan fragment. Den andra halvan kan replikeras till en annan fragment. 

Hur data replikeras beror på replikeringsfaktorn. Med en replikeringsfaktor på 2 har du två kopior av varje bit data över dina tre shards i fragmentgruppen. På samma sätt replikeras alla data i varje fragment med en replikeringsfaktor på 3 och tre shards i fragmentgruppen. Varje fragment i fragmentgruppen kan ha olika replikeringsfaktor. Den här inställningen hjälper dig att definiera din design med hög tillgänglighet och haveriberedskap effektivt inom en fragmentgrupp och över flera shardgrupper.

I föregående arkitektur innehåller fragmentgrupp A och fragmentgrupp B båda samma data men finns i olika tillgänglighetszoner. Om både fragmentgrupp A och fragmentgrupp B har samma replikeringsfaktor på 3 replikeras varje rad/segment i den fragmenterade tabellen sex gånger över de två fragmentgrupperna. Om fragmentgrupp A har en replikeringsfaktor på 3 och fragmentgrupp B har en replikeringsfaktor på 2, replikeras varje rad/segment 5 gånger över de två fragmentgrupperna.

Den här inställningen förhindrar dataförlust om ett fel på instansnivå eller tillgänglighetszonnivå inträffar. Programlagret kan läsa från och skriva till varje fragment. För att minimera konflikter anger Oracle Sharding ett "huvudsegment" för varje intervall av hash-värden. Den här funktionen säkerställer att skrivbegäranden för ett visst segment dirigeras till motsvarande segment. Dessutom tillhandahåller Oracle GoldenGate automatisk konfliktidentifiering och konfliktlösning för att hantera eventuella konflikter som kan uppstå. Mer information och begränsningar för implementering av GoldenGate med Oracle Sharding finns i Oracles dokumentation om hur du använder [Oracle GoldenGate med en fragmenterad databas](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

I föregående arkitektur distribueras en GSM/shard-chef i alla tillgänglighetszoner för hög tillgänglighet. Rekommendationen är att distribuera minst en GSM/shard director per datacenter eller region. Dessutom distribueras en instans av programservern i alla tillgänglighetszoner som innehåller en fragmentgrupp. Med den här inställningen kan programmet hålla svarstiden mellan programservern och databasen/fragmentgruppen låg. Om en databas misslyckas kan programservern i samma zon som väntedatabasen hantera begäranden när databasrollen har övergått. Azure Application Gateway och shard-chefen håller reda på begäran och svarssvarstid och vägbegäranden därefter.

Från ett programperspektiv gör klientsystemet en begäran till Azure Application Gateway (eller andra belastningsutjämningstekniker i Azure) som omdirigerar begäran till den region som är närmast klienten. Azure Application Gateway stöder också klibbiga sessioner, så alla begäranden som kommer från samma klient dirigeras till samma programserver. Programservern använder anslutningspoolning i dataåtkomstdrivrutiner. Den här funktionen är tillgänglig i drivrutiner som JDBC, ODP.NET, OCI, etc. Drivrutinerna kan känna igen sharding nycklar som anges som en del av begäran. [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) för JDBC-klienter kan göra det möjligt för icke-Oracle-programklienter som Apache Tomcat och IIS att arbeta med Oracle Sharding. 

Under den första begäran ansluter programservern till fragmenthanteraren i regionen för att hämta routningsinformation för shard som begäran måste dirigeras till. Baserat på den sharding-nyckel som skickades dirigerar direktören programservern till respektive shard. Programservern cachelagrar den här informationen genom att skapa en karta och för efterföljande begäranden kringgår fragmentregissören och routsbegäranden direkt till shard.

## <a name="patching-and-maintenance"></a>Korrigering och underhåll

När du distribuerar dina Oracle-arbetsbelastningar till Azure tar Microsoft hand om alla korrigeringar på värd-OS-nivå. Allt planerat underhåll på OS-nivå meddelas kunderna i förväg för att kunden ska kunna underhållas. Två servrar från två olika tillgänglighetszoner korrigeras aldrig samtidigt. Se [Hantera tillgängligheten för virtuella datorer](../../../virtual-machines/linux/manage-availability.md) för mer information om vm-underhåll och korrigering. 

Korrigering av operativsystemet för virtuella datorer kan automatiseras med [Azure Automation](../../../automation/automation-tutorial-update-management.md). Korrigering och underhåll av Oracle-databasen kan automatiseras och schemaläggas med [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) eller [Azure Automation](../../../automation/automation-tutorial-update-management.md) för att minimera driftstopp. Se [Kontinuerlig leverans och blå/grön distribution för](/azure/devops/learn/what-is-continuous-delivery) att förstå hur den kan användas i samband med oracle-databaser.

## <a name="architecture-and-design-considerations"></a>Arkitektur- och designöverväganden

- Överväg att använda hypertrådade [minnesoptimerade virtuella datorer](../../../virtual-machines/windows/sizes-memory.md) med [begränsade centrala virtuella processorer](../../../virtual-machines/windows/constrained-vcpu.md) för din virtuella Oracle-databas för att spara på licenskostnader och maximera prestanda. Använd flera premium- eller ultradiskar (hanterade diskar) för prestanda och tillgänglighet.
- När du använder hanterade diskar kan disk-/enhetsnamnet ändras vid omstarter. Vi rekommenderar att du använder enhetens UUID i stället för namnet för att se till att dina fästen kvarstår över omstarter. Mer information finns [här](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab).
- Använd tillgänglighetszoner för att uppnå hög tillgänglighet i regionen.
- Överväg att använda ultradiskar (när sådana är tillgängliga) eller premiumdiskar för Oracle-databasen.
- Överväg att konfigurera en Oracle-databas i vänteläge i en annan Azure-region med Oracle Data Guard.
- Överväg att använda [närhetsplaceringsgrupper](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) för att minska svarstiden mellan programmet och databasnivån.
- Konfigurera [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) för hantering, övervakning och loggning.
- Överväg att använda Oracle Automatic Storage Management (ASM) för strömlinjeformad lagringshantering för databasen.
- Använd [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) för att hantera korrigering och uppdateringar av databasen utan avbrott.
- Justera din programkod för att lägga till molnbaserade mönster som [återförsöksmönster,](/azure/architecture/patterns/retry) [kretsbrytarmönster](/azure/architecture/patterns/circuit-breaker)och andra mönster som definierats i [guiden För molndesignmönster](/azure/architecture/patterns/) som kan hjälpa ditt program att bli mer flexibelt.

## <a name="next-steps"></a>Nästa steg

Granska följande Oracle-referensartiklar som gäller för ditt scenario.

- [Introduktion till Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle Data Guard Mäklare Begrepp](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Konfigurera Oracle GoldenGate för aktiv aktiv hög tillgänglighet](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Översikt över Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard Far Sync Zero Data Loss på alla avstånd](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
