---
title: Vad är tjänsten Azure SQL Database?
description: 'Få en introduktion till SQL Database: teknisk information och funktioner i Microsoft Relations databas hanterings system (RDBMS) i molnet.'
keywords: introduktion till sql, sql-introduktion, vad är sql-databas
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
ms.openlocfilehash: d22408904ae21bc7512d4ebe8792666227893337
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687023"
---
# <a name="what-is-the-azure-sql-database-service"></a>Vad är tjänsten Azure SQL Database?

Azure SQL Database är en Relations databas för generell användning, som tillhandahålls som en hanterad tjänst. Med den kan du skapa ett hög tillgängligt och högpresterande data lagrings lager för programmen och lösningarna i Azure. SQL Database kan vara det rätta valet för en mängd moderna moln program eftersom det gör att du kan bearbeta både Relations data och [icke-relationella strukturer](sql-database-multi-model-features.md), till exempel grafer, JSON, spatial och XML.

Den är baserad på den senaste stabila versionen av [Microsoft SQL Server databas motorn](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Du kan använda avancerade funktioner för frågekörning, till exempel [högpresterande minnes teknologier](sql-database-in-memory.md) och [intelligent bearbetning av frågor](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). I själva verket lanseras de nyaste funktionerna i SQL Server först till SQL Database och sedan SQL Server sig själva. Du får de senaste SQL Server funktionerna utan kostnader för uppdatering eller uppgradering, testat över miljon tals databaser. 

Med SQL Database kan du enkelt definiera och skala prestanda inom två olika inköps modeller: en [vCore-baserad inköps modell](sql-database-service-tiers-vcore.md) och en [DTU-baserad inköps modell](sql-database-service-tiers-dtu.md). SQL Database är en fullständigt hanterad tjänst som har inbyggd hög tillgänglighet, säkerhets kopiering och andra vanliga underhålls åtgärder. Microsoft hanterar alla korrigeringar och uppdateringar av SQL-och operativ system koden. Du behöver inte hantera den underliggande infrastrukturen.

> [!NOTE]
> För relevanta villkor och deras definitioner, se ord listan för [SQL Database termer](sql-database-glossary-terms.md).

## <a name="deployment-models"></a>Distributionsmodeller

Azure SQL Database innehåller följande distributionsalternativ för en Azure SQL-databas:

![Diagram över distributions alternativ](./media/sql-database-technical-overview/deployment-options.png)

- En [enskild databas](sql-database-single-database.md) representerar en fullständigt hanterad, isolerad databas. Du kan använda det här alternativet om du har moderna moln program och mikrotjänster som behöver en enda tillförlitlig data källa. En enda databas liknar en [innesluten databas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) i [Microsoft SQL Server databas motor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [Hanterad instans](sql-database-managed-instance.md) är en fullständigt hanterad instans av [Microsoft SQL Server databas motorn](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Den innehåller en uppsättning databaser som kan användas tillsammans. Använd det här alternativet för enkel migrering av lokala SQL Server databaser till Azure-molnet och för program som behöver använda de databas funktioner som SQL Server databas motorn tillhandahåller.
- [Elastisk pool](sql-database-elastic-pool.md) är en samling av [enskilda databaser](sql-database-single-database.md) med en delad uppsättning resurser, till exempel processor eller minne. Enskilda databaser kan flyttas till och från en elastisk pool.

> [!IMPORTANT]
> Om du vill förstå funktions skillnaderna mellan SQL Database och SQL Server, och skillnaderna mellan olika Azure SQL Database distributions alternativ, se [SQL Database funktioner](sql-database-features.md).

SQL Database levererar förutsägbara prestanda med flera resurs typer, tjänst nivåer och beräknings storlekar. Den ger dynamisk skalbarhet utan drift avbrott, inbyggd intelligent optimering, global skalbarhet och tillgänglighet och avancerade säkerhets alternativ. Med de här funktionerna kan du fokusera på snabb app-utveckling och påskynda din tid till marknad, i stället för att hantera virtuella datorer och infrastruktur. SQL Databases tjänsten är för närvarande i 38-datacenter runtom i världen, så att du kan köra databasen i ett Data Center nära dig.

## <a name="scalable-performance-and-pools"></a>Skalbar prestanda och pooler

Du kan definiera mängden tilldelade resurser. 
- Med enkla databaser är varje databas isolerad från andra och är portabel. Var och en har sin egen garanterade mängd beräknings-, minnes-och lagrings resurser. Mängden resurser som är tilldelade till databasen är dedikerad till databasen och delas inte med andra databaser i Azure. Du kan skala upp och ned [enkla databas resurser](sql-database-single-database-scale.md) dynamiskt. Alternativet enkel databas ger olika data bearbetnings-, minnes-och lagrings resurser för olika behov. Du kan till exempel hämta 1 till 80 virtuella kärnor eller 32 GB till 4 TB. Med den [storskaliga tjänst nivån](sql-database-service-tier-hyperscale.md) för en enskild databas kan du skala till 100 TB, med snabba funktioner för säkerhets kopiering och återställning.
- Med elastiska pooler kan du tilldela resurser som delas av alla databaser i poolen. Du kan skapa en ny databas eller flytta befintliga enkla databaser till en resurspool för att maximera användningen av resurser och spara pengar. Med det här alternativet får du också möjlighet att dynamiskt [skala elastiska pool resurser](sql-database-elastic-pool-scale.md) upp och ned.
- Med hanterade instanser isoleras varje hanterad instans från andra instanser med garanterade resurser. I en hanterad instans delar instans databaserna en uppsättning resurser. Du kan [skala hanterade instans resurser](sql-database-managed-instance-resource-limits.md) dynamiskt.

Du kan bygga din första app på en liten, enkel databas till en låg kostnad per månad i tjänst nivån för generell användning. Du kan sedan ändra dess tjänst nivå manuellt eller program mässigt när som helst till den verksamhets kritiska tjänst nivån, för att uppfylla behoven hos din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser som du behöver när du behöver dem.

*Dynamisk skalbarhet* skiljer sig från *autoskalning*. Med Autoskala avses när en tjänst skalar automatiskt utifrån olika kriterier, medan dynamisk skalbarhet möjliggör manuell skalning utan avbrott. Alternativet enkel databas stöder manuell dynamisk skalbarhet, men inte autoskalning. Om du vill ha en mer automatisk upplevelse bör du överväga att använda elastiska pooler som gör att databaser kan dela resurser i en pool baserat på enskilda databas behov. Ett annat alternativ är att använda skript som kan hjälpa dig att automatisera skalbarheten för en enskild databas. Ett exempel finns i [använda PowerShell för att övervaka och skala en enskild databas](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Köpmodeller

SQL Database erbjuder följande inköps modeller:
- Med den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md) kan du välja antalet virtuella kärnor, mängden minne och lagrings hastigheten. Med den vCore-baserade inköps modellen kan du också använda [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) för att få kostnads besparingar. Mer information om Azure Hybrid-förmån finns i avsnittet Vanliga frågor och svar längre fram i den här artikeln.
- Den [DTU-baserade inköps modellen](sql-database-service-tiers-dtu.md) erbjuder en blandning av beräknings-, minnes-och i/O-resurser i tre tjänst nivåer för att stödja ljus till tung databas arbets belastningar. Beräknings storlekar inom varje nivå ger en annan blandning av dessa resurser, där du kan lägga till ytterligare lagrings resurser.
- Den [serverbaserade modellen](sql-database-serverless.md) skalar automatiskt beräkning baserat på arbets belastnings behov och fakturerar för mängden data bearbetning som används per sekund. Server lös beräknings nivån pausar också automatiskt databaser under inaktiva perioder när endast lagring faktureras och återupptar automatiskt databaser när aktiviteten returnerar.

### <a name="service-tiers"></a>Tjänstnivåer

Azure SQL Database erbjuder tre tjänst nivåer som är utformade för olika typer av program:
- [Generell användning/standard](sql-database-service-tier-general-purpose.md) service nivå som är avsedd för vanliga arbets belastningar. Det erbjuder budgetorienterade balanserade beräknings-och lagrings alternativ.
- [Affärskritisk/Premium](sql-database-service-tier-business-critical.md) Service Tier som är utformad för OLTP-program med hög transaktions hastighet och I/O för lägsta svars tid. Den ger den högsta återhämtningen till problem genom att använda flera isolerade repliker.
- [Storskalig](sql-database-service-tier-hyperscale.md) Service Tier som är utformad för mycket stor OLTP-databas och möjlighet att skala lagrings utrymme och skala automatiskt. 

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastiska pooler som maximerar resursutnyttjandet

För många företag och appar räcker det att kunna skapa enkla databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Oförutsägbara användnings mönster kan göra det svårt att hantera kostnader och din affärs modell. [Elastiska pooler](sql-database-elastic-pool.md) är utformade för att lösa detta problem. Du allokerar prestanda resurser till en pool i stället för en enskild databas. Du betalar för poolens samlade prestanda resurser snarare än för enkel databas prestanda.

   ![Bild som visar elastiska pooler i Basic-, standard-och Premium-versioner](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Med elastiska pooler behöver du inte fokusera på att reglera databasprestanda upp och ner när behovet av resurser varierar. Pooldatabaserna förbrukar den elastiska poolens prestandaresurser efter behov. Pooldatabaser förbrukar, men överskrider inte begränsningarna i poolen. Dina kostnader förblir förutsägbara även om den individuella databasanvändningen inte är det.

Du kan [lägga till och ta bort databaser i poolen](sql-database-elastic-pool-manage-portal.md)och skala din app från en fåtal databaser till tusentals, allt inom en budget som du styr. Du kan också kontrol lera minimi-och Max resurserna som är tillgängliga för databaser i poolen, för att säkerställa att ingen databas i poolen använder alla pool resurser och att varje databas i poolen har en garanterad minimal mängd resurser. Mer information om design mönster för SaaS-program (program vara som en tjänst) som använder elastiska pooler finns i [design mönster för SaaS-program med flera innehavare med SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Skript kan hjälpa dig med att övervaka och skala elastiska pooler. Ett exempel finns i [använda PowerShell för att övervaka och skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> En hanterad instans har inte stöd för elastiska pooler. En hanterad instans är i stället en samling instans databaser som delar hanterade instans resurser.

### <a name="blend-single-databases-with-pooled-databases"></a>Blanda enkla databaser med pooldatabaser

Du kan blanda enskilda databaser med elastiska pooler och ändra tjänst nivåerna för enskilda databaser och elastiska pooler för att anpassa sig till din situation. Du kan också kombinera och matcha andra Azure-tjänster med SQL Database för att möta dina unika design behov för appar, öka kostnaderna och resurs effektiviteten och låsa upp nya affärs möjligheter.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Omfattande övervakning och aviseringsfunktioner

Azure SQL Database tillhandahåller avancerade övervaknings-och fel söknings funktioner som hjälper dig att få djupare insikt i arbets Belastningens egenskaper. Följande funktioner och verktyg är:
 - De inbyggda övervaknings funktioner som tillhandahålls av den senaste versionen av SQL Server Database Engine. De gör att du kan hitta prestanda insikter i real tid. 
 - PaaS övervaknings funktioner som tillhandahålls av Azure och som hjälper dig att övervaka och felsöka ett stort antal databas instanser.

[Query Store](sql-database-operate-query-store.md), en inbyggd SQL Server övervaknings funktion, registrerar prestanda för dina frågor i real tid och gör det möjligt att identifiera potentiella prestanda problem och de främsta resurs förbrukarna. Automatisk justering och rekommendationer ger råd om frågor med försämrat prestanda och saknade eller duplicerade index. Med automatisk justering i SQL Database kan du antingen manuellt tillämpa skript som kan åtgärda problemen eller låta SQL Database tillämpa korrigeringen. SQL Database kan också testa och kontrol lera att korrigeringen ger viss förmån och behålla eller återställa ändringen beroende på resultatet. Förutom fråge Arkiv och automatiska justerings funktioner kan du använda standard [DMV: er och XEvent](sql-database-monitoring-with-dmvs.md) för att övervaka arbets Belastningens prestanda.

Azure innehåller [inbyggda verktyg för prestanda övervakning](sql-database-performance.md) och [avisering](sql-database-insights-alerts-portal.md) , kombinerat med prestanda klassificeringar som gör att du kan övervaka status för tusentals databaser. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ned baserat på dina aktuella eller planerade prestanda behov. Dessutom kan SQL-databasen [skapa mått och diagnostikloggar](sql-database-metrics-diag-logging.md) för lättare övervakning. Du kan konfigurera SQL-databasen för att lagra resursanvändning, personal och sessioner och anslutning till en av dessa Azure-resurser:

- **Azure Storage**: För arkivering av stora mängder telemetridata till ett lågt pris.
- **Azure Event Hubs**: för att integrera SQL Database telemetri med din anpassade övervaknings lösning eller aktiva pipeliner.
- **Azure Monitor loggar**: för en inbyggd övervaknings lösning med rapporter, aviseringar och mildrande funktioner.

![Diagram över Azures övervaknings arkitektur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Kapacitet för tillgänglighet

I en traditionell SQL Servers miljö har du normalt minst två datorer som är lokalt konfigurerade. De här datorerna har exakta, synkront underhåll, kopior av data för att skydda mot ett haveri på en enskild dator eller komponent. Den här miljön ger hög tillgänglighet, men den skyddar inte mot en natur haveri förstör ditt data Center.

Haveri beredskap förutsätter att en oåterkallelig händelse är geografiskt lokaliserad för att ha en annan dator eller uppsättning datorer med en kopia av dina data långt bort. I SQL Server kan du använda Always on-tillgänglighetsgrupper som körs i asynkront läge för att få den här funktionen. Användare vill ofta inte vänta på att replikeringen ska ske innan den genomför en transaktion, så det är möjligt att förlora data när du gör oplanerad redundans.

Databaser i tjänst nivåerna Premium och Business Critical gör det redan [något liknande](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) för synkroniseringen av en tillgänglighets grupp. Databaser med lägre tjänst nivåer ger redundans genom att använda en [annan men likvärdig mekanism](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Inbyggd logik skyddar mot ett enskilt dator haveri. Funktionen aktiv geo-replikering ger dig möjlighet att skydda mot katastrofer där en hel region förstörs.

Azure-tillgänglighetszoner försöker skydda mot avbrott i en enda data centers byggnad inom en enda region. Det hjälper dig att skydda mot förlust av ström eller nätverk till en byggnad. I SQL Database placerar du de olika replikerna i olika tillgänglighets zoner (olika byggnader, effektivt).

Service avtalet [(SLA)](https://azure.microsoft.com/support/legal/sla/) för Azure, som drivs av ett globalt nätverk av Microsoft-hanterade data Center, hjälper till att hålla appen igång 24/7. Azure-plattformen hanterar fullständigt alla databaser och garanterar ingen data förlust och en hög procent andel data tillgänglighet. Azure hanterar automatiskt korrigering, säkerhets kopiering, replikering, fel identifiering, underliggande potentiell maskin vara, program vara eller nätverks fel, distribution av fel korrigeringar, redundans, databas uppgraderingar och andra underhålls aktiviteter. Standardtillgänglighet uppnås genom en uppdelning av beräknings och lagringslager. Premium-tillgänglighet uppnås genom att integrera beräkning och lagring på en enda nod för prestanda och sedan implementera teknik som liknar Always on-tillgänglighetsgrupper. En fullständig beskrivning av funktionerna för hög tillgänglighet i Azure SQL Database finns [SQL Database tillgänglighet](sql-database-high-availability.md). 

Dessutom ger SQL Database inbyggd [affärs kontinuitet och globala skalbarhets](sql-database-business-continuity.md) funktioner. Exempel på dessa är:

- [Automatisk säkerhets kopiering](sql-database-automated-backups.md):

  SQL Database automatiskt utför fullständiga, differentiella och transaktions logg säkerhets kopior av SQL-databaser så att du kan återställa till vilken tidpunkt som helst. För enskilda databaser och databaser i pooler kan du konfigurera SQL Database att lagra fullständiga säkerhets kopior av databasen till Azure Storage för långsiktig kvarhållning av säkerhets kopior. För hanterade instanser kan du även utföra säkerhets kopieringar för långsiktig kvarhållning av säkerhets kopior.

- [Återställning av](sql-database-recovery-using-backups.md)tidpunkt:

  Alla SQL Database distributions alternativ stöder återställning till vilken tidpunkt som helst inom den automatiska kvarhållningsperioden för säkerhets kopiering för alla SQL-databaser.
- [Aktiv geo-replikering](sql-database-active-geo-replication.md):

  Med alternativen för databas och pooler med flera databaser kan du konfigurera upp till fyra läsbara sekundära databaser i antingen samma eller globalt distribuerade Azure-datacenter. Om du till exempel har ett SaaS-program med en katalog databas som har en stor mängd samtidiga skrivskyddade transaktioner, använder du aktiv geo-replikering för att aktivera global Läs skalning. Detta tar bort Flask halsar på den primära som beror på Läs arbets belastningar. För hanterade instanser använder du grupper för automatisk redundans.
- [Grupper för automatisk redundans](sql-database-auto-failover-group.md):

  Med alla SQL Database distributions alternativ kan du använda grupper för växling vid fel för att aktivera hög tillgänglighet och belastnings utjämning i global skala. Detta inkluderar transparent geo-replikering och redundans av stora mängder databaser, elastiska pooler och hanterade instanser. Med växlings grupper kan du skapa globalt distribuerade SaaS-program med minimal administration. Detta lämnar all komplex övervakning, Routning och redundanskluster till SQL Database.
- [Zoner-redundanta databaser](sql-database-high-availability.md):

  Med SQL Database kan du etablera Premium-eller affärs kritiska databaser eller elastiska pooler i flera tillgänglighets zoner. Eftersom dessa databaser och elastiska pooler har flera redundanta repliker för hög tillgänglighet ger repliker i flera tillgänglighets zoner högre återhämtning. Detta inkluderar möjligheten att återställa automatiskt från data centers skalnings fel utan data förlust.

## <a name="built-in-intelligence"></a>Inbyggd intelligens

Med SQL Database får du inbyggd information som hjälper dig att dramatiskt minska kostnaderna för att köra och hantera databaser och som maximerar programmets prestanda och säkerhet. Genom att köra miljon tals kund arbets belastningar dygnet runt, SQL Database samla in och bearbeta en enorm mängd telemetridata, samtidigt som kunden är helt respekterad. Olika algoritmer utvärderar kontinuerligt telemetri-data så att tjänsten kan lära sig och anpassa sig till ditt program.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisk prestandaövervakning och justering

SQL Database ger detaljerad inblick i frågor som du behöver övervaka. SQL Database lär dig mer om dina databas mönster och gör det möjligt att anpassa ditt databas schema till din arbets belastning. SQL-databasen innehåller [rekommendationer för prestandajustering](sql-database-advisor.md) där du kan granska justeringsåtgärder och använda dem.

Att ständigt övervaka en databas är dock en svår och omständlig uppgift, särskilt när du hanterar många databaser. [Intelligent Insights](sql-database-intelligent-insights.md) gör jobbet åt dig genom att automatiskt övervaka SQL Database prestanda i hög skala. Det informerar om prestanda försämrings problem, den identifierar den bakomliggande orsaken till varje problem och ger rekommendationer om prestanda förbättring när det är möjligt.

Att hantera ett stort antal databaser kan vara omöjligt att effektivisera även med alla tillgängliga verktyg och rapporter som SQL Database och Azure tillhandahåller. I stället för att övervaka och justera databasen manuellt, kan du delegera vissa av övervaknings-och justerings åtgärderna till SQL Database med hjälp av [Automatisk justering](sql-database-automatic-tuning.md). SQL Database använder automatiskt rekommendationer, tester och verifierar var och en av dess justerings åtgärder för att säkerställa att prestandan förbättras. På så sätt kan SQL Database automatiskt anpassas efter din arbets belastning på ett kontrollerat och säkert sätt. Automatisk justering innebär att databasens prestanda övervakas noga och jämförs före och efter varje justerings åtgärd. Om prestandan inte förbättras återställs justerings åtgärden.

Många av våra partner som kör [SaaS för flera klient](sql-database-design-patterns-multi-tenancy-saas-applications.md) organisationer ovanpå SQL Database förlitar sig på automatisk prestanda justering för att säkerställa att deras program alltid har stabila och förutsägbara prestanda. För dessa minskar funktionen risken för en prestandaincident mitt i natten. Eftersom en del av deras kund Base också använder SQL Server, använder de samma indexerings rekommendationer som tillhandahålls av SQL Database för att hjälpa sina SQL Server kunder.

Det finns två automatiska justerings aspekter [i SQL Database](sql-database-automatic-tuning.md):

- **Automatisk indexhantering**: Identifierar index som bör läggas till i din databas och index som ska tas bort.
- **Automatisk plan korrigering**: identifierar problematiska planer och åtgärdar prestanda problem i SQL-plan.

### <a name="adaptive-query-processing"></a>Anpassningsbar frågebearbetning

Du kan använda [anpassad bearbetning av frågor](/sql/relational-databases/performance/intelligent-query-processing), inklusive överlagrad körning av tabell värdes funktioner med flera instruktioner, återkoppling av minnes bidrag i batch-läge och anpassningsbara kopplingar i batch-läge. De olika funktionerna med anpassningsbar frågebearbetning tillämpar liknande ”Läs och anpassa”-teknik, vilket bidrar till förmågan att hantera prestandaproblem som berör historiskt olösbara optimeringsproblem.

## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad

SQL-databasen innehåller en uppsättning [inbyggda funktioner för säkerhet och efterlevnad](sql-database-security-overview.md) för att uppfylla olika krav för säkerhet och regelefterlevnad i programmet.

> [!IMPORTANT]
> Microsoft har certifierat Azure SQL Database (alla distributions alternativ) mot ett antal efterlevnads standarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), där du hittar den mest aktuella listan med SQL Database certifierings certifieringar.

### <a name="advance-threat-protection"></a>Skydd mot Avancerat skydd

Avancerad data säkerhet är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Den innehåller funktioner för att identifiera och klassificera känsliga data, hantera dina databas sårbarheter och identifiera avvikande aktiviteter som kan tyda på ett hot mot databasen. Det ger en enda plats för att aktivera och hantera dessa funktioner.

- [Data identifiering och klassificering](sql-database-data-discovery-and-classification.md):

  Den här funktionen innehåller funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera, märka och skydda känsliga data i dina databaser. Den ger insyn i databas klassificerings tillstånd och spårar åtkomsten till känsliga data i databasen och utanför dess gränser.
- [Sårbarhets bedömning](sql-vulnerability-assessment.md):

  Den här tjänsten kan upptäcka, spåra och hjälpa dig att åtgärda eventuella sårbarheter i databasen. Den ger inblick i dina säkerhetstillstånd och inkluderar lämpliga åtgärder för att lösa säkerhetsproblem och förbättra databasens skydd.
- [Hot identifiering](sql-database-threat-detection.md):

  Den här funktionen identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databasen. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhetsaviseringar om potentiella säkerhetsproblem, SQL-inmatningsattacker samt avvikande åtkomstmönster i databasen. Aviseringar om hot identifiering ger information om den misstänkta aktiviteten och rekommenderar åtgärder för att undersöka och minimera hotet.

### <a name="auditing-for-compliance-and-security"></a>Granskning för efterlevnad och säkerhet

[Granskning](sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg i ditt Azure Storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databas aktivitet och få insikt i avvikelser och avvikelser som kan tyda på affärs problem eller misstänkta säkerhets överträdelser.

### <a name="data-encryption"></a>Datakryptering

SQL Database hjälper till att skydda dina data genom att tillhandahålla kryptering. För data i rörelse används [Transport Layer Security](https://support.microsoft.com/kb/3135244). För data i vila används [transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). För data som används använder den [alltid krypterad](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med SQL Database kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory-integrering](sql-database-aad-authentication.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md) för att öka säkerheten för data och program, samtidigt som den har stöd för en enda inloggnings process.

## <a name="easy-to-use-tools"></a>Lättanvända verktyg

SQL Database gör byggandet och underhållet av appar enklare och mer produktivt. Med SQL Database kan du fokusera på det du gör väst: bygga bra appar. Du kan hantera och utveckla i SQL Database med hjälp av verktyg och färdigheter som du redan har.

- [Azure Portal](https://portal.azure.com/):

  Ett webbaserat program för att hantera alla Azure-tjänster.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Ett kostnads fritt, nedladdnings Bart klient program för att hantera alla SQL-infrastrukturer, från SQL Server till SQL Database.
- [SQL Server data tools i Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  Ett kostnads fritt, nedladdnings Bart klient program för att utveckla SQL Server Relations databaser, SQL-databaser, Integration Services-paket, Analysis Services data modeller och repor ting Services-rapporter.
- [Visual Studio Code](https://code.visualstudio.com/docs):

  En kostnads fri, nedladdnings bar kod redigerare med öppen källkod för Windows, macOS och Linux. Det stöder tillägg, inklusive [MSSQL-tillägget](https://aka.ms/mssql-marketplace) för att fråga Microsoft SQL Server, Azure SQL Database och Azure SQL Data Warehouse.

SQL Database har stöd för att skapa program med python, Java, Node. js, PHP, ruby och .NET på macOS, Linux och Windows. SQL Database stöder samma [anslutningsbibliotek](sql-database-libraries.md) som SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Vanliga frågor och svar om SQL Database

### <a name="what-is-the-current-version-of-sql-database"></a>Vilken är den aktuella versionen av SQL Database?

Den aktuella versionen av SQL Database är V12. Version V11 har dragits tillbaka.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Kan jag styra när uppdaterings stopp inträffar?

Nej. Effekten av korrigeringen är vanligt vis inte märkbart om du [använder logik för omprövning](sql-database-develop-overview.md#resiliency) i din app. Mer information finns i [Planera för underhålls händelser i Azure i Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid-förmån frågor

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Finns det dubbla användnings rättigheter med Azure Hybrid-förmån för SQL Server?

Du har 180 dagar med dubbla användnings rättigheter för licensen för att säkerställa att migreringen körs sömlöst. Efter den 180-dagars perioden kan du bara använda SQL Server-licensen i molnet i SQL Database. Du har inte längre dubbla användnings rättigheter lokalt och i molnet.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hur skiljer sig Azure Hybrid-förmån för SQL Server från License Mobility?

Vi erbjuder License Mobility-förmåner för att SQL Server kunder med Software Assurance. Detta gör det möjligt att omtilldela licenser till en partners delade servrar. Du kan använda den här förmånen på Azure IaaS och AWS EC2.

Azure Hybrid-förmån för SQL Server skiljer sig från licens mobilitet i två viktiga områden:

- Det ger ekonomiska fördelar för att flytta hög virtualiserade arbets belastningar till Azure. SQL Server Enterprise Edition-kunder kan få fyra kärnor i Azure i Generell användning SKU för varje kärna som de äger lokalt för mycket virtualiserade program. License Mobility tillåter inte några särskilda kostnads besparingar för att flytta virtualiserade arbets belastningar till molnet.
- Det tillhandahåller ett PaaS-mål på Azure (SQL Database Hanterad instans) som är mycket kompatibel med SQL Server lokalt.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Vilka är de speciella rättigheterna för Azure Hybrid-förmån för SQL Server?

SQL Database kunder har följande rättigheter som är kopplade till Azure Hybrid-förmån för SQL Server:

|Licensavtryck|Vad får Azure Hybrid-förmån för SQL Server dig?|
|---|---|
|SQL Server Enterprise Edition Core-kunder med SA|<li>Kan betala bas priset antingen Generell användning eller Affärskritisk SKU</li><br><li>1 kärna lokalt = 4 kärnor i Generell användning SKU</li><br><li>1 kärna lokalt = 1 kärna i Affärskritisk SKU</li>|
|SQL Server Standard Edition Core-kunder med SA|<li>Kan betala bas taxan endast för Generell användning SKU</li><br><li>1 kärna lokalt = 1 kärna i Generell användning SKU</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Tala med teknikteamet för SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Ställ frågor om databas administration.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Ställ frågor om utveckling.
- [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Ställ tekniska frågor.
- [Feedback](https://aka.ms/sqlfeedback): rapportera buggar och begär ande funktion.
- [Reddit](https://www.reddit.com/r/SQLServer/): diskutera SQL Server.

## <a name="next-steps"></a>Nästa steg

- På [sidan med priser](https://azure.microsoft.com/pricing/details/sql-database/) hittar du kostnads jämförelser och räknare för enskilda databaser och elastiska pooler.
- Kom igång genom att se följande snabb starter:

  - [Skapa en SQL-databas i Azure Portal](sql-database-single-database-get-started.md)  
  - [Skapa en SQL-databas med Azure CLI](sql-database-get-started-cli.md)
  - [Skapa en SQL Database med PowerShell](sql-database-get-started-powershell.md)

- En uppsättning Azure CLI- och PowerShell-exempel finns här:
  - [Azure CLI-exempel för SQL Database](sql-database-cli-samples.md)
  - [Azure PowerShell-exempel för SQL Database](sql-database-powershell-samples.md)

- Information om nya funktioner när de visas finns i Azure- [Översikt för SQL Database](https://azure.microsoft.com/roadmap/?category=databases).
- Se [Azure SQL Database blogg](https://azure.microsoft.com/blog/topics/database)där SQL Server produkt Teams medlemmar bloggar om SQL Database nyheter och funktioner.

