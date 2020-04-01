---
title: Vad är tjänsten Azure SQL Database?
description: 'Få en introduktion till SQL Database: teknisk information och funktioner för Microsofts relationsdatabashanteringssystem (RDBMS) i molnet.'
keywords: introduktion till sql, sql-introduktion, vad är sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 0d50ddbbeeaed48c14d07c42588efcbb20bb7d79
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411161"
---
# <a name="what-is-the-azure-sql-database-service"></a>Vad är tjänsten Azure SQL Database?

Azure SQL Database är en fullständigt hanterad PaaS-databasmotor (plattform som en tjänst) som hanterar de flesta databashanteringsfunktioner, till exempel uppgradering, korrigering, säkerhetskopiering och övervakning utan användarmedverkan. Azure SQL Database körs alltid på den senaste stabila versionen av SQL Server Database-motorn och ett uppdaterat operativsystem med 99,99 % tillgänglighet. Med PaaS-funktioner som är inbyggda i Azure SQL-databasen kan du fokusera på de domänspecifika databasadministrations- och optimeringsaktiviteter som är viktiga för ditt företag.

Med Azure SQL Database kan du skapa ett högtillgänge och högpresterande datalagringslager för program och lösningar i Azure. SQL Database kan vara rätt val för en mängd moderna molnprogram eftersom det gör att du kan bearbeta både relationsdata och [icke-relationsstrukturer](sql-database-multi-model-features.md), till exempel diagram, JSON, spatial och XML.

Den är baserad på den senaste stabila versionen av [Microsoft SQL Server-databasmotorn](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Du kan använda avancerade frågebearbetningsfunktioner, till exempel [högpresterande minnesteknik](sql-database-in-memory.md) och [intelligent frågebearbetning](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). Faktum är att de senaste funktionerna i SQL Server först släpps till SQL Database och sedan till SQL Server själv. Du får de senaste SQL Server-funktionerna utan omkostnader för korrigering eller uppgradering, testad i miljontals databaser. 

Med SQL Database kan du enkelt definiera och skala prestanda inom två olika inköpsmodeller: en [vCore-baserad inköpsmodell](sql-database-service-tiers-vcore.md) och en [DTU-baserad inköpsmodell](sql-database-service-tiers-dtu.md). SQL Database är en fullständigt hanterad tjänst som har inbyggd hög tillgänglighet, säkerhetskopior och andra vanliga underhållsåtgärder. Microsoft hanterar all korrigering och uppdatering av SQL- och operativsystemskoden. Du behöver inte hantera den underliggande infrastrukturen.

> [!NOTE]
> Relevanta termer och deras definitioner finns i [ordlistan för SQL-databastermer](sql-database-glossary-terms.md).

## <a name="deployment-models"></a>Distributionsmodeller

Azure SQL Database innehåller följande distributionsalternativ för en Azure SQL-databas:

![Diagram över distributionsalternativ](./media/sql-database-technical-overview/deployment-options.png)

- [En enda databas](sql-database-single-database.md) representerar en fullständigt hanterad, isolerad databas. Du kan använda det här alternativet om du har moderna molnprogram och mikrotjänster som behöver en enda tillförlitlig datakälla. En enskild databas liknar en [databas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) i [Microsoft SQL Server Database Engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [Hanterad instans](sql-database-managed-instance.md) är en fullständigt hanterad instans av [Microsoft SQL Server Database Engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Den innehåller en uppsättning databaser som kan användas tillsammans. Använd det här alternativet för enkel migrering av lokala SQL Server-databaser till Azure-molnet och för program som behöver använda databasfunktionerna som SQL Server Database Engine tillhandahåller.
- [Elastisk pool](sql-database-elastic-pool.md) är en samling [enskilda databaser](sql-database-single-database.md) med en delad uppsättning resurser, till exempel CPU eller minne. Enskilda databaser kan flyttas till och från en elastisk pool.

> [!IMPORTANT]
> Information om hur du förstår funktionsskillnaderna mellan SQL Database och SQL Server, samt skillnaderna mellan olika distributionsalternativ för Azure SQL Database, finns i [SQL Database-funktioner](sql-database-features.md).

SQL Database ger förutsägbar prestanda med flera resurstyper, tjänstnivåer och beräkningsstorlekar. Det ger dynamisk skalbarhet utan driftstopp, inbyggd intelligent optimering, global skalbarhet och tillgänglighet samt avancerade säkerhetsalternativ. Med de här funktionerna kan du fokusera på snabb apputveckling och påskynda din time-to-market, snarare än på att hantera virtuella datorer och infrastruktur. SQL Database-tjänsten finns för närvarande i 38 datacenter runt om i världen, så att du kan köra databasen i ett datacenter nära dig.

## <a name="scalable-performance-and-pools"></a>Skalbar prestanda och pooler

Du kan definiera mängden resurser som tilldelats. 
- Med enstaka databaser är varje databas isolerad från andra och bärbar. Var och en har sin egen garanterade mängd beräknings-, minnes- och lagringsresurser. Mängden resurser som tilldelas databasen är dedikerad till databasen och delas inte med andra databaser i Azure. Du kan dynamiskt [skala enskilda databasresurser](sql-database-single-database-scale.md) upp och ned. Alternativet med en databas innehåller olika beräknings-, minnes- och lagringsresurser för olika behov. Du kan till exempel få 1 till 80 virtuella kärnor eller 32 TILL 4 TB. Den [hyperskala tjänstnivån](sql-database-service-tier-hyperscale.md) för en enda databas kan du skala till 100 TB, med snabb säkerhetskopiering och återställning kapacitet.
- Med elastiska pooler kan du tilldela resurser som delas av alla databaser i poolen. Du kan skapa en ny databas eller flytta befintliga enskilda databaser till en resurspool för att maximera resursanvändningen och spara pengar. Det här alternativet ger dig också möjlighet att dynamiskt [skala elastiska poolresurser](sql-database-elastic-pool-scale.md) upp och ned.
- Med hanterade instanser isoleras varje hanterad instans från andra instanser med garanterade resurser. I en hanterad instans delar instansdatabaserna en uppsättning resurser. Du kan [skala hanterade instansresurser](sql-database-managed-instance-resource-limits.md) dynamiskt uppåt och nedåt.

Du kan skapa din första app på en liten, enskild databas till en låg kostnad per månad på den allmänna servicenivån. Du kan sedan ändra dess tjänstnivå manuellt eller programmässigt när som helst till den affärskritiska tjänstnivån för att uppfylla behoven hos din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser som du behöver när du behöver dem.

*Dynamisk skalbarhet* skiljer sig från *automatisk skalning*. Med Autoskala avses när en tjänst skalar automatiskt utifrån olika kriterier, medan dynamisk skalbarhet möjliggör manuell skalning utan avbrott. Alternativet med en databas stöder manuell dynamisk skalbarhet, men inte automatisk skalning. Om du vill ha en mer automatisk upplevelse bör du använda elastiska pooler, vilka tillåter databaser att dela resurser i en pool utifrån enskilda databasbehov. Ett annat alternativ är att använda skript som kan hjälpa till att automatisera skalbarheten för en enskild databas. Ett exempel finns i [Använda PowerShell för att övervaka och skala en enskild databas](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Köpmodeller

SQL Database erbjuder följande inköpsmodeller:
- Med den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) kan du välja antalet virtuella kärnor, mängden minne och mängden och lagringshastigheten. Den vCore-baserade inköpsmodellen gör att du också kan använda [Azure Hybrid Benefit för SQL Server för](https://azure.microsoft.com/pricing/hybrid-benefit/) att få kostnadsbesparingar. Mer information om Azure Hybrid-förmånen finns i avsnittet "Vanliga frågor" senare i den här artikeln.
- Den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) erbjuder en blandning av beräknings-, minnes- och I/O-resurser på tre tjänstnivåer för att stödja lätta till tunga databasarbetsbelastningar. Beräkningsstorlekar inom varje nivå ger en annan blandning av dessa resurser, till vilka du kan lägga till ytterligare lagringsresurser.
- Den [serverlösa modellen](sql-database-serverless.md) skalar automatiskt beräkning baserat på arbetsbelastningsbehov och räkningar för mängden beräkning som används per sekund. Den serverlösa beräkningsnivån pausar också automatiskt databaser under inaktiva perioder när endast lagringsutrymme faktureras och återupptar automatiskt databaser när aktiviteten returnerar.

### <a name="service-tiers"></a>Tjänstnivåer

Azure SQL Database erbjuder tre tjänstnivåer som är utformade för olika typer av program:
- [Tjänstnivå för allmänt syfte/standard](sql-database-service-tier-general-purpose.md) som utformats för vanliga arbetsbelastningar. Det erbjuder budgetorienterade balanserade beräknings- och lagringsalternativ.
- [Business Critical/Premium-tjänstnivå](sql-database-service-tier-business-critical.md) utformad för OLTP-program med hög transaktionsfrekvens och i/o med lägsta latens. Det ger högsta motståndskraft mot fel med hjälp av flera isolerade repliker.
- [Hyperskala](sql-database-service-tier-hyperscale.md) tjänstnivå utformad för mycket stora OLTP-databas och möjligheten att automatiskt skala lagring och skala beräkning smidigt.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastiska pooler som maximerar resursutnyttjandet

För många företag och appar räcker det att kunna skapa enkla databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Oförutsägbara användningsmönster kan göra det svårt att hantera kostnader och din affärsmodell. [Elastiska pooler](sql-database-elastic-pool.md) är utformade för att lösa detta problem. Du allokerar prestandaresurser till en pool i stället för en enskild databas. Du betalar för poolens kollektiva prestandaresurser i stället för för enskilda databasprestanda.

   ![Grafik som visar elastiska pooler i grundläggande, standard och premium utgåvor](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Med elastiska pooler behöver du inte fokusera på att ringa upp och ner databasprestanda när efterfrågan på resurser varierar. Pooldatabaserna förbrukar den elastiska poolens prestandaresurser efter behov. Poolade databaser förbrukar men överskrider inte gränserna för poolen, så kostnaden förblir förutsägbar även om den enskilda databasanvändningen inte gör det.

Du kan [lägga till och ta bort databaser i poolen](sql-database-elastic-pool-manage-portal.md)och skala appen från en handfull databaser till tusentals, allt inom en budget som du styr. Du kan också styra de lägsta och högsta resurser som är tillgängliga för databaser i poolen för att säkerställa att ingen databas i poolen använder alla poolresurser och att varje poolad databas har ett garanterat minsta antal resurser. Mer information om designmönster för SaaS-program (Software as a Service) som använder elastiska pooler finns i [Designmönster för SaaS-program med flera innehavare med SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Skript kan hjälpa dig med att övervaka och skala elastiska pooler. Ett exempel finns i [Använda PowerShell för att övervaka och skala en ELASTISK SQL-pool i Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> En hanterad instans stöder inte elastiska pooler. En hanterad instans är snarare en samling instansdatabaser som delar hanterade instansresurser.

### <a name="blend-single-databases-with-pooled-databases"></a>Blanda enkla databaser med pooldatabaser

Du kan blanda enskilda databaser med elastiska pooler och ändra tjänstnivåerna för enskilda databaser och elastiska pooler för att anpassa sig till din situation. Du kan också blanda och matcha andra Azure-tjänster med SQL Database för att uppfylla dina unika appdesignbehov, öka kostnaderna och resurseffektiviteten och låsa upp nya affärsmöjligheter.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Omfattande övervakning och aviseringsfunktioner

Azure SQL Database innehåller avancerade övervaknings- och felsökningsfunktioner som hjälper dig att få djupare insikter om arbetsbelastningsegenskaper. Dessa funktioner och verktyg inkluderar:
 - De inbyggda övervakningsfunktionerna i den senaste versionen av SQL Server Database Engine. De gör att du kan hitta prestandainsikter i realtid. 
 - PaaS-övervakningsfunktioner som tillhandahålls av Azure och som gör att du kan övervaka och felsöka ett stort antal databasinstanser.

[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), en inbyggd SQL Server-övervakningsfunktion, registrerar prestanda för dina frågor i realtid och gör att du kan identifiera potentiella prestandaproblem och de främsta resurskonsumenterna. Automatisk justering och rekommendationer ger råd om frågor med regressed prestanda och saknade eller duplicerade index. Med automatisk justering i SQL Database kan du antingen manuellt använda skripten som kan åtgärda problemen eller låta SQL Database använda korrigeringen. SQL Database kan också testa och verifiera att korrigeringen ger viss nytta och behålla eller återställa ändringen beroende på resultatet. Förutom Query Store och automatiska justeringsfunktioner kan du använda vanliga [DMV:er och XEvent](sql-database-monitoring-with-dmvs.md) för att övervaka arbetsbelastningsprestandan.

Azure tillhandahåller [inbyggda verktyg för prestandaövervakning](sql-database-performance-guidance.md) och [aviseringar,](sql-database-insights-alerts-portal.md) i kombination med prestandaklassificeringar, som gör att du kan övervaka statusen för tusentals databaser. Med hjälp av dessa verktyg kan du snabbt bedöma effekten av att skala upp eller ned, baserat på dina aktuella eller beräknade prestandabehov. Dessutom kan SQL-databasen [skapa mått och diagnostikloggar](sql-database-metrics-diag-logging.md) för lättare övervakning. Du kan konfigurera SQL-databasen för att lagra resursanvändning, personal och sessioner och anslutning till en av dessa Azure-resurser:

- **Azure Storage**: För arkivering av stora mängder telemetri till ett litet pris.
- **Azure Event Hubs**: För integrering av SQL Database-telemetri med din anpassade övervakningslösning eller aktiva pipelines.
- **Azure Monitor loggar:** För en inbyggd övervakningslösning med rapportering, aviseringar och förmildrande funktioner.

![Diagram över Azure-övervakningsarkitektur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Kapacitet för tillgänglighet

Azure SQL Database gör det möjligt för ditt företag att fortsätta fungera under avbrott. I en traditionell SQL Server-miljö har du i allmänhet minst två datorer lokalt konfigurerade. Dessa maskiner har exakta, synkront underhållna, kopior av data för att skydda mot ett fel på en enda maskin eller komponent. Den här miljön ger hög tillgänglighet, men den skyddar inte mot en naturkatastrof som förstör ditt datacenter.

Haveriberedskap förutsätter att en katastrofal händelse är geografiskt lokaliserad nog att ha en annan dator eller uppsättning datorer med en kopia av dina data långt borta. I SQL Server kan du använda Alltid på tillgänglighetsgrupper som körs i async-läge för att få den här funktionen. Människor vill ofta inte vänta på att replikeringen ska ske så långt bort innan de genomför en transaktion, så det finns risk för dataförlust när du gör oplanerade redundans.

Databaser på premium- och affärskritiska tjänstnivåer gör redan [något som liknar](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) synkroniseringen av en tillgänglighetsgrupp. Databaser på lägre tjänstnivåer ger redundans via lagring med hjälp av en [annan men likvärdig mekanism](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Inbyggd logik hjälper till att skydda mot ett enda datorfel. Den aktiva georeplikeringsfunktionen ger dig möjlighet att skydda dig mot en katastrof där en hel region förstörs.

Azure-tillgänglighetszoner försöker skydda mot avbrott i en enda datacenterbyggnad inom en enda region. Det hjälper dig att skydda mot förlust av ström eller nätverk till en byggnad. I SQL Database placerar du de olika replikerna i olika tillgänglighetszoner (olika byggnader, effektivt).

Faktum är att servicenivåavtalet [(SLA)](https://azure.microsoft.com/support/legal/sla/) för Azure, som drivs av ett globalt nätverk av Microsoft-hanterade datacenter, hjälper till att hålla din app igång 24/7. Azure-plattformen hanterar alla databaser helt och hållet och garanterar ingen dataförlust och en hög procentandel av datatillgängligheten. Azure hanterar automatiskt korrigeringar, säkerhetskopior, replikering, felidentifiering, underliggande potentiell maskinvara, programvara eller nätverksfel, distribution av buggfixar, redundans, databasuppgraderingar och andra underhållsuppgifter. Standardtillgänglighet uppnås genom en uppdelning av beräknings och lagringslager. Premium-tillgänglighet uppnås genom att integrera beräkning och lagring på en enda nod för prestanda och sedan implementera teknik som liknar alltid på tillgänglighetsgrupper. En fullständig diskussion om funktionerna för hög tillgänglighet i Azure SQL Database finns i [SQL Database-tillgängligheten](sql-database-high-availability.md). 

Dessutom ger SQL Database inbyggd [affärskontinuitet och globala skalbarhetsfunktioner.](sql-database-business-continuity.md) Exempel på dessa är:

- [Automatiska säkerhetskopior:](sql-database-automated-backups.md)

  SQL Database utför automatiskt fullständiga, differentiella och transaktionsloggsäkerheter av SQL-databaser så att du kan återställa till valfri tidpunkt. För enskilda databaser och poolade databaser kan du konfigurera SQL Database för att lagra fullständiga säkerhetskopieringar av databaser till Azure Storage för långsiktig lagring av säkerhetskopiering. För hanterade instanser kan du också utföra säkerhetskopior med endast kopior för långsiktig lagring av säkerhetskopiering.

- [Point-in-time återställer:](sql-database-recovery-using-backups.md)

  Alla distributionsalternativ för SQL-databas stöder återställning till valfri tidpunkt inom den automatiska lagringsperioden för säkerhetskopiering för alla SQL-databaser.
- [Aktiv geo-replikering:](sql-database-active-geo-replication.md)

  Med alternativen för en databas och poolade databaser kan du konfigurera upp till fyra läsbara sekundära databaser i antingen samma eller globalt distribuerade Azure-datacenter. Om du till exempel har ett SaaS-program med en katalogdatabas som har en hög volym samtidiga skrivskyddade transaktioner använder du aktiv georeplikering för att aktivera global lässkala. Detta tar bort flaskhalsar på den primära som beror på läsarbetsbelastningar. Använd grupper för automatisk redundans för hanterade instanser.
- [Grupper för automatisk redundans:](sql-database-auto-failover-group.md)

  Med alla distributionsalternativ för SQL-databas kan du använda redundansk grupper för att aktivera hög tillgänglighet och belastningsutjämning på global nivå. Detta inkluderar transparent geo-replikering och redundans av stora uppsättningar databaser, elastiska pooler och hanterade instanser. Redundansk grupper gör det möjligt att skapa globalt distribuerade SaaS-program, med minimal administration omkostnader. Detta lämnar all komplex övervakning, routning och redundansorkestrering till SQL Database.
- [Zonundanta databaser:](sql-database-high-availability.md)

  Med SQL Database kan du etablera premium- eller affärskritiska databaser eller elastiska pooler i flera tillgänglighetszoner. Eftersom dessa databaser och elastiska pooler har flera redundanta repliker för hög tillgänglighet, ger placering av dessa repliker i flera tillgänglighetszoner högre återhämtning. Detta inkluderar möjligheten att återställa automatiskt från datacenterskalefel, utan dataförlust.

## <a name="built-in-intelligence"></a>Inbyggd intelligens

Med SQL Database får du inbyggd intelligens som hjälper dig att dramatiskt minska kostnaderna för att köra och hantera databaser, och som maximerar både prestanda och säkerhet för ditt program. Sql Database kör miljontals kundarbetsbelastningar dygnet runt och samlar in och bearbetar en enorm mängd telemetridata, samtidigt som kundsekretessen respekteras fullt ut. Olika algoritmer utvärderar kontinuerligt telemetridata så att tjänsten kan lära sig och anpassa sig med ditt program.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisk prestandaövervakning och justering

SQL Database ger detaljerad inblick i frågor som du behöver övervaka. SQL Database lär sig om dina databasmönster och gör att du kan anpassa databasschemat till din arbetsbelastning. SQL-databasen innehåller [rekommendationer för prestandajustering](sql-database-advisor.md) där du kan granska justeringsåtgärder och använda dem.

Men att ständigt övervaka en databas är en svår och tråkig uppgift, särskilt när det handlar om många databaser. [Intelligent Insights](sql-database-intelligent-insights.md) gör det här jobbet åt dig genom att automatiskt övervaka SQL Database-prestanda i stor skala. Den informerar dig om prestandaförsämringsproblem, identifierar orsaken till varje problem och ger rekommendationer för prestandaförbättring när det är möjligt.

Att hantera ett stort antal databaser kan vara omöjligt att göra effektivt även med alla tillgängliga verktyg och rapporter som SQL Database och Azure tillhandahåller. I stället för att övervaka och justera databasen manuellt kan du överväga att delegera några av övervaknings- och justeringsåtgärderna till SQL Database med hjälp av [automatisk justering](sql-database-automatic-tuning.md). SQL Database tillämpar automatiskt rekommendationer, tester och verifierar var och en av sina justeringsåtgärder för att säkerställa att prestandan förbättras. På så sätt anpassar SQL Database automatiskt till din arbetsbelastning på ett kontrollerat och säkert sätt. Automatisk justering innebär att databasens prestanda övervakas noggrant och jämförs före och efter varje justering. Om prestandan inte förbättras återställs justeringsåtgärden.

Många av våra partner som kör [SaaS appar](sql-database-design-patterns-multi-tenancy-saas-applications.md) med flera innehavare ovanpå SQL Database förlitar sig på automatisk prestandajustering för att se till att deras program alltid har stabila och förutsägbara prestanda. För dessa minskar funktionen risken för en prestandaincident mitt i natten. Eftersom en del av deras kundbas också använder SQL Server använder de dessutom samma indexeringsrekommendationer som tillhandahålls av SQL Database för att hjälpa sina SQL Server-kunder.

Två automatiska justeringsaspekter [finns i SQL Database:](sql-database-automatic-tuning.md)

- **Automatisk indexhantering**: Identifierar index som bör läggas till i din databas och index som ska tas bort.
- **Automatisk plankorrigering**: Identifierar problematiska planer och åtgärdar prestandaproblem för SQL-planen.

### <a name="adaptive-query-processing"></a>Anpassningsbar frågebearbetning

Du kan använda [adaptiv frågebearbetning,](/sql/relational-databases/performance/intelligent-query-processing)inklusive interfolierad körning för tabellvärderade funktioner med flera utdrag, feedback om minnesbidrag för batchläge och anpassningskopplingar för batchläge. Var och en av dessa adaptiva frågebearbetningsfunktioner tillämpar liknande "lär och anpassar" tekniker, vilket ytterligare hjälper till att lösa prestandaproblem relaterade till historiskt svårlösta frågeoptimeringsproblem.

## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad

SQL-databasen innehåller en uppsättning [inbyggda funktioner för säkerhet och efterlevnad](sql-database-security-overview.md) för att uppfylla olika krav för säkerhet och regelefterlevnad i programmet.

> [!IMPORTANT]
> Microsoft har certifierad Azure SQL Database (alla distributionsalternativ) mot ett antal efterlevnadsstandarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), där du hittar den senaste listan över SQL Database-efterlevnadscertifieringar.

### <a name="advance-threat-protection"></a>Skydd mot hot i förväg

Avancerad datasäkerhet är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Den innehåller funktioner för att upptäcka och klassificera känsliga data, hantera databassårbarheter och identifiera avvikande aktiviteter som kan tyda på ett hot mot databasen. Det ger en enda plats för att aktivera och hantera dessa funktioner.

- [Data upptäckt och klassificering:](sql-database-data-discovery-and-classification.md)

  Den här funktionen innehåller funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera, etikettera och skydda känsliga data i dina databaser. Den ger insyn i databasklassificeringstillståndet och spårar åtkomsten till känsliga data i databasen och utanför dess gränser.
- [Sårbarhetsbedömning:](sql-vulnerability-assessment.md)

  Den här tjänsten kan identifiera, spåra och hjälpa dig att åtgärda potentiella databassårbarheter. Den ger inblick i dina säkerhetstillstånd och inkluderar lämpliga åtgärder för att lösa säkerhetsproblem och förbättra databasens skydd.
- [Upptäckt av hot:](sql-database-threat-detection.md)

  Den här funktionen identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databasen. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhetsaviseringar om potentiella säkerhetsproblem, SQL-inmatningsattacker samt avvikande åtkomstmönster i databasen. Varningar för identifiering av hot innehåller information om den misstänkta aktiviteten och rekommenderar åtgärder för hur du undersöker och minskar hotet.

### <a name="auditing-for-compliance-and-security"></a>Granskning för efterlevnad och säkerhet

[Granskning](sql-database-auditing.md) spårar databashändelser och skriver dem till en granskningslogg i ditt Azure-lagringskonto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktivitet och få insikt i avvikelser och avvikelser som kan tyda på affärsproblem eller misstänkta säkerhetsöverträdelser.

### <a name="data-encryption"></a>Datakryptering

SQL Database hjälper till att skydda dina data genom att tillhandahålla kryptering. För data i rörelse används [säkerhet för transportlager](https://support.microsoft.com/kb/3135244). För data i vila använder den [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). För data som används används den [alltid krypterad](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med SQL Database kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory-integrering](sql-database-aad-authentication.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [multifaktorautentisering](sql-database-ssms-mfa-authentication.md) för att öka data- och programsäkerheten, samtidigt som den stöder en enda inloggningsprocess.

## <a name="easy-to-use-tools"></a>Lättanvända verktyg

SQL Database gör byggandet och underhållet av appar enklare och mer produktivt. Med SQL Database kan du fokusera på det du gör väst: bygga bra appar. Du kan hantera och utveckla i SQL Database med hjälp av verktyg och färdigheter som du redan har.

- [Azure-portalen:](https://portal.azure.com/)

  Ett webbaserat program för att hantera alla Azure-tjänster.
- [SQL Server Management Studio:](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

  Ett kostnadsfritt, nedladdningsbart klientprogram för hantering av alla SQL-infrastrukturer, från SQL Server till SQL Database.
- [SQL Server-dataverktyg i Visual Studio:](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

  Ett kostnadsfritt, nedladdningsbart klientprogram för att utveckla SQL Server relationsdatabaser, SQL-databaser, Integration Services-paket, Analysis Services-datamodeller och Reporting Services-rapporter.
- [Visual Studio-kod:](https://code.visualstudio.com/docs)

  En gratis, nedladdningsbar, öppen källkodsredigerare för Windows, macOS och Linux. Den stöder tillägg, inklusive [mssql-tillägget](https://aka.ms/mssql-marketplace) för att fråga Microsoft SQL Server, Azure SQL Database och Azure SQL Data Warehouse.

SQL Database stöder att skapa program med Python, Java, Node.js, PHP, Ruby och .NET på macOS, Linux och Windows. SQL Database stöder samma [anslutningsbibliotek](sql-database-libraries.md) som SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Vanliga frågor och svar i SQL-databasen

### <a name="what-is-the-current-version-of-sql-database"></a>Vad är den aktuella versionen av SQL Database?

Den aktuella versionen av SQL Database är V12. Version V11 har dragits tillbaka.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Kan jag kontrollera när korrigeringstopp inträffar?

Nej. Effekten av korrigering är i allmänhet inte märkbar om du [använder återförsökslogik](sql-database-develop-overview.md#resiliency) i din app. Mer information finns i [Planera för Underhåll av Azure-underhåll i Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Frågor om Azure Hybrid-förmåner

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Finns det dubbla användningsområden med Azure Hybrid Benefit för SQL Server?

Du har 180 dagars rättigheter för dubbla användningsområden för licensen för att säkerställa att migreringar körs sömlöst. Efter den 180-dagarsperioden kan du bara använda SQL Server-licensen i molnet i SQL Database. Du har inte längre behörigheter för dubbla användningsområden lokalt och i molnet.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hur skiljer sig Azure Hybrid Benefit för SQL Server från licensmobilitet?

Vi erbjuder licensmobilitetsförmåner till SQL Server-kunder med Software Assurance. Detta gör det möjligt att tilldela sina licenser till en partners delade servrar. Du kan använda den här förmånen på Azure IaaS och AWS EC2.

Azure Hybrid Benefit för SQL Server skiljer sig från licensmobilitet inom två nyckelområden:

- Det ger ekonomiska fördelar för att flytta mycket virtualiserade arbetsbelastningar till Azure. SQL Server Enterprise Edition-kunder kan få fyra kärnor i Azure i SKU för allmänt syfte för alla kärnor de äger lokalt för mycket virtualiserade program. Licensmobilitet tillåter inte några särskilda kostnadsfördelar för att flytta virtualiserade arbetsbelastningar till molnet.
- Det ger en PaaS-mål på Azure (SQL Database managed instance) som är mycket kompatibel med SQL Server lokalt.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Vilka är de specifika rättigheterna för Azure Hybrid Benefit för SQL Server?

SQL Database-kunder har följande rättigheter som är associerade med Azure Hybrid Benefit för SQL Server:

|Licensavtryck|Vad får Azure Hybrid Benefit för SQL Server dig?|
|---|---|
|SQL Server Enterprise Edition-kärnkunder med SA|<li>Kan betala basränta på antingen allmänt syfte eller affärskritiska SKU</li><br><li>1 kärna lokalt = 4 kärnor i Allmänt Ändamål SKU</li><br><li>1 kärna lokalt = 1 kärna i Affärskritiska SKU</li>|
|SQL Server Standard Edition-kärnkunder med SA|<li>Kan endast betala basränta på SKU för allmänt ändamål</li><br><li>1 kärna lokalt = 1 kärna i Allmänt Ändamål SKU</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Tala med teknikteamet för SQL Server

- [DBA Stack Exchange:](https://dba.stackexchange.com/questions/tagged/sql-server)Ställ frågor databasadministration.
- [Stack överflöd:](https://stackoverflow.com/questions/tagged/sql-server)Ställ utvecklingsfrågor.
- [MSDN Forum](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Ställ tekniska frågor.
- [Feedback:](https://aka.ms/sqlfeedback)Rapportera fel och begäran funktionen.
- [Reddit](https://www.reddit.com/r/SQLServer/): Diskutera SQL Server.

## <a name="next-steps"></a>Nästa steg

- Se [prissidan](https://azure.microsoft.com/pricing/details/sql-database/) för kostnadsjämställande och kalkylatorer för enskilda databaser och elastiska pooler.
- Se de här snabbstarterna för att komma igång:

  - [Skapa en SQL-databas i Azure Portal](sql-database-single-database-get-started.md)  
  - [Skapa en SQL-databas med Azure CLI](sql-database-get-started-cli.md)
  - [Skapa en SQL Database med PowerShell](sql-database-get-started-powershell.md)

- En uppsättning Azure CLI- och PowerShell-exempel finns här:
  - [Azure CLI-exempel för SQL Database](sql-database-cli-samples.md)
  - [Azure PowerShell-exempel för SQL Database](sql-database-powershell-samples.md)

- Information om nya funktioner när de meddelas finns i [Azure Roadmap for SQL Database](https://azure.microsoft.com/roadmap/?category=databases).
- Se [Azure SQL Database-bloggen](https://azure.microsoft.com/blog/topics/database), där SQL Server-produktgruppmedlemmar bloggar om SQL Database-nyheter och -funktioner.

