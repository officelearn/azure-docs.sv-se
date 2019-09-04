---
title: Vad är tjänsten Azure SQL Database? | Microsoft Docs
description: 'Få en introduktion till SQL Database: teknisk information och funktioner i Microsofts relationella databashanteringssystem (RDBMS) i molnet.'
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
ms.openlocfilehash: 8c931521e77123844817dff0e9209f8f95f7ad59
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279905"
---
# <a name="what-is-azure-sql-database-service"></a>Vad är Azure SQL Database tjänst

Azure SQL Database är en hanterad tjänst för allmän Relations databas som gör att du kan skapa ett data lagrings lager med hög tillgänglighet och hög prestanda för program och lösningar i Microsoft Azure molnet. SQL Database kan vara det rätta valet för en mängd moderna moln program, eftersom det gör att du kan använda kraftfulla funktioner för bearbetning av både Relations data och [icke-relationella strukturer](sql-database-multi-model-features.md) som grafer, JSON, spatial och XML. Den är baserad på den senaste stabila versionen av [Microsoft SQL Server Database Engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) och gör att du kan använda en omfattande uppsättning funktioner för avancerad frågekörning, till exempel [högpresterande minnes teknologier](sql-database-in-memory.md) och [intelligent Query-bearbetning ](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json).
Med Microsofts moln-först-strategi släpps de senaste funktionerna för SQL Server först till SQL-databasen och sedan till SQL-servern. Den här metoden ger de senaste funktionerna för SQL Server utan merkostnader för uppdatering eller uppgradering och med nya funktioner som testas över miljontals databaser. Med SQL Database kan du enkelt definiera och skala prestanda inom två olika inköps modeller: en [vCore-baserad inköps modell](sql-database-service-tiers-vcore.md) och en [DTU-baserad inköps modell](sql-database-service-tiers-dtu.md). SQL Database är en fullständigt hanterad tjänst som har inbyggda hög tillgänglighet, säkerhets kopieringar och andra vanliga underhålls åtgärder. Microsoft hanterar alla korrigeringar och uppdateringar av SQL och OS-koden sömlöst och sammanfattar all hantering av den underliggande infrastrukturen.

> [!NOTE]
> En ord lista med termer i Azure SQL Database finns i [ord lista för SQL Database termer](sql-database-glossary-terms.md)

Azure SQL Database innehåller följande distributionsalternativ för en Azure SQL-databas:

![distributionsalternativ](./media/sql-database-technical-overview/deployment-options.png)

- En [enskild databas](sql-database-single-database.md) representerar en fullständigt hanterad isolerad databas som är perfekt för de moderna moln program och mikrotjänster som behöver en enda tillförlitlig data källa. En enda databas liknar en [innesluten databas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) i [Microsoft SQL Server databas motor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [Hanterad instans](sql-database-managed-instance.md) är en fullständigt hanterad instans av [Microsoft SQL Server databas motor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) som innehåller en uppsättning databaser som kan användas tillsammans. Det är ett perfekt val för enkel migrering av lokala SQL Server-databaser till Azure-molnet och för program som behöver utnyttja kraftfulla databas funktioner som SQL Server databas motorn tillhandahåller.
- [Elastisk pool](sql-database-elastic-pool.md) är en samling av [enskilda databaser](sql-database-single-database.md) med en delad uppsättning resurser, till exempel processor eller minne. Enskilda databaser kan flyttas till och från en elastisk pool.

> [!IMPORTANT]
> För att förstå funktions skillnaderna mellan SQL Database och SQL Server, samt skillnaderna mellan olika Azure SQL Database distributions alternativ, se SQL- [funktioner](sql-database-features.md).

SQL Database levererar förutsägbar prestanda med flera resurs typer, tjänst nivåer och beräknings storlekar som ger dynamisk skalbarhet utan drift avbrott, inbyggd intelligent optimering, global skalbarhet och tillgänglighet samt avancerad säkerhet alternativ – allt med nästan noll administration. Dessa funktioner gör att du kan fokusera på snabb apputveckling och att accelerera din tid till marknaden, istället för att ägna värdefull tid och resurser åt att hantera virtuella datorer och infrastruktur. SQL Database-tjänsten används för närvarande i 38 datacenter runtom i världen och många fler datacenter ansluter sig hela tiden, vilket innebär att du kan köra din databas från ett datacenter nära dig.

## <a name="scalable-performance-and-pools"></a>Skalbar prestanda och pooler

Med alla varianter av SQL Database kan du definiera hur mycket resurser som ska tilldelas. 
- Med enkla databaser är varje databas isolerad från varandra och portabla, var och en med sin egen garanterade mängd beräknings-, minnes-och lagrings resurser. Mängden resurser som är kopplade till databasen är dedikerad till databasen och kommer inte att delas med andra databaser i Azure-molnet. Du får också möjlighet att dynamiskt [skala enkla databas resurser](sql-database-single-database-scale.md) uppåt och nedåt. Enkel databas tillhandahåller olika beräknings-, minnes-och lagrings resurser för olika behov som skiljer sig från 1 till 80 virtuella kärnor, 32 GB till 4 TB osv. Med den [storskaliga tjänst nivån](sql-database-service-tier-hyperscale.md) för en enskild databas kan du skala till 100 TB, med snabba funktioner för säkerhets kopiering och återställning.
- Med elastiska pooler kan du tilldela resurser som ska delas av alla databaser i poolen. Du kan skapa en ny databas eller flytta befintliga enkla databaser till en resurspool för att maximera användningen av resurser och spara pengar – och möjligheten att dynamiskt [skala elastiska pool resurser](sql-database-elastic-pool-scale.md) upp och ned.
- Med hanterade instanser isoleras varje hanterad instans från andra instanser med garanterade resurser. I en hanterad instans delar instans databaserna en uppsättning resurser – och möjligheten att dynamiskt [skala hanterade instans resurser](sql-database-managed-instance-resource-limits.md) upp och ned.

Du kan bygga din första app på en liten, enkel databas till en låg kostnad per månad i den allmänna tjänst nivån, och sedan ändra dess tjänst nivå manuellt eller program mässigt när som helst till den verksamhets kritiska tjänst nivån som uppfyller lösningens behov. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

Dynamisk skalbarhet skiljer sig från autoskalning. Med Autoskala avses när en tjänst skalar automatiskt utifrån olika kriterier, medan dynamisk skalbarhet möjliggör manuell skalning utan avbrott. En enkel databas stöder manuell dynamisk skalbarhet, men inte autoskalning. Om du vill ha en mer *automatisk* upplevelse bör du använda elastiska pooler, vilka tillåter databaser att dela resurser i en pool utifrån enskilda databasbehov. Det finns dock skript som kan hjälpa dig att automatisera skalbarheten för en enskild databas. Ett exempel finns i [använda PowerShell för att övervaka och skala en enskild databas](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>Inköps modeller, tjänst nivåer, beräknings storlek och lagrings belopp

SQL Database erbjuder två inköps modeller:
- Med den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md) kan du välja antalet virtuella kärnor, mängden eller minnet samt mängden och lagrings hastigheten. Med den vCore-baserade inköps modellen kan du också använda [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) för att få kostnads besparingar. Mer information om Azure Hybrid-förmån finns i [vanliga frågor och svar](#sql-database-frequently-asked-questions-faq).
- Den [DTU-baserade inköps modellen](sql-database-service-tiers-dtu.md) erbjuder en blandning av beräknings-, minnes-och IO-resurser i tre tjänst nivåer för att stödja lätta till tunga databas arbets belastningar. Beräknings storlekar inom varje nivå ger en annan blandning av dessa resurser, där du kan lägga till ytterligare lagrings resurser.

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastiska pooler som maximerar resursutnyttjandet

För många företag och appar räcker det att kunna skapa enkla databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. [Elastiska pooler](sql-database-elastic-pool.md) är utformade för att lösa detta problem. Konceptet är enkelt. Du allokerar prestanda resurser till en pool i stället för en enskild databas och betalar för de sammanslagna prestanda resurserna i poolen snarare än för enkel databas prestanda.

   ![elastiska pooler](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Med elastiska pooler behöver du inte fokusera på att reglera databasprestanda upp och ner när behovet av resurser varierar. Pooldatabaserna förbrukar den elastiska poolens prestandaresurser efter behov. Pooldatabaser förbrukar, men överskrider inte begränsningarna i poolen. Dina kostnader förblir förutsägbara även om den individuella databasanvändningen inte är det. Dessutom kan du [lägga till och ta bort databaser i poolen](sql-database-elastic-pool-manage-portal.md) och därmed skala din app från några få databaser till tusentals, inom en budget som du själv styr över. Du också styra de minimalt och maximalt tillgängliga resurser som är tillgängliga för databaser i poolen, för att säkerställa att ingen databas i poolen använder alla poolresurser och att varje pooldatabas har garanterade resurser. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Skript kan hjälpa dig med att övervaka och skala elastiska pooler. Mer information finns i [Använda PowerShell till att övervaka och skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> En hanterad instans har inte stöd för elastiska pooler. En hanterad instans är i stället en samling instans databaser som delar hanterade instans resurser.

### <a name="blend-single-databases-with-pooled-databases"></a>Blanda enkla databaser med pooldatabaser

Du kan blanda enkla databaser med elastiska pooler och ändra tjänst nivåerna för enskilda databaser och elastiska pooler snabbt och enkelt för att anpassa sig till din situation. Med den kraft och räckvidd som Azure har, kan du dessutom blanda andra Azure-tjänster med SQL Database, allt för att möta designbehoven för just din unika app, samt styra kostnader och upptäcka nya affärsmöjligheter.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Omfattande övervakning och aviseringsfunktioner

Azure SQL Database innehåller en uppsättning avancerade övervaknings-och fel söknings funktioner som kan hjälpa dig att få fullständiga insikter om arbets Belastningens egenskaper. Funktionerna och verktygen kan kategoriseras som:
 - De inbyggda övervaknings funktioner som tillhandahålls av den senaste versionen av SQL Server Database Engine som gör att du kan hitta prestanda insikter i real tid. 
 - PaaS övervaknings funktioner som tillhandahålls av Azure-plattformen och som gör att du enkelt kan övervaka ett stort antal databas instanser och även tillhandahålla fel söknings råd som kan hjälpa dig att åtgärda prestanda problem.

Den viktigaste inbyggda övervaknings funktionen för databas motorn som du bör använda är en [frågekomponent](sql-database-operate-query-store.md) som registrerar prestanda för dina frågor i real tid och gör att du kan identifiera potentiella prestanda problem och den översta resursen kunderna. Automatisk justering och rekommendationer ger råd om frågor med försämrat prestanda och saknade eller duplicerade index. Med automatisk justering i Azure SQL Database kan du antingen manuellt tillämpa skript som kan åtgärda problemen eller låta Azure SQL Database att tillämpa korrigeringen, testa och verifiera ger den en del förmån och behålla eller återställa ändringen beroende på resultatet. Förutom fråge Arkiv och automatiska justerings funktioner kan du också använda standard [DMV: er och XEvent](sql-database-monitoring-with-dmvs.md) för att övervaka arbets Belastningens prestanda.

Azure-plattformen tillhandahåller [inbyggda verktyg för prestanda övervakning](sql-database-performance.md) och [avisering](sql-database-insights-alerts-portal.md) , kombinerat med prestanda klassificeringen, som gör att du enkelt kan övervaka status för tusentals databaser. Med hjälp av dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ned baserat på dina aktuella eller beräknade prestanda behov. Dessutom kan SQL-databasen [skapa mått och diagnostikloggar](sql-database-metrics-diag-logging.md) för lättare övervakning. Du kan konfigurera SQL-databasen för att lagra resursanvändning, personal och sessioner och anslutning till en av dessa Azure-resurser:

- **Azure Storage**: För arkivering av stora mängder telemetri för ett litet pris.
- **Azure Event Hub**: För att integrera SQL Database telemetri med din anpassade övervaknings lösning eller aktiva pipeliner.
- **Azure Monitor loggar**: För inbyggd övervaknings lösning med rapportering, avisering och minskning av funktioner.

    ![Arkitektur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Kapacitet för tillgänglighet

I en traditionell SQL Server-miljö skulle du vanligt vis ha (minst) 2 datorer som har kon figurer ATS lokalt med exakta (synkront underhållna) kopior av data (med funktioner som AlwaysOn-tillgänglighetsgrupper eller instanser av kluster instanser) för att skydda mot en Det gick inte att utföra en enskild dator/komponent. Detta ger hög tillgänglighet, men skyddar inte mot en natur katastrof som förstör ditt data Center.

Haveri beredskap förutsätter att en oåterkallelig händelse kommer att vara geografiskt lokaliserad för att ha en annan dator eller uppsättning datorer med en kopia av dina data långt bort.  I SQL Server kan du använda Always on-tillgänglighetsgrupper som körs i asynkront läge för att få den här funktionen.  Hastigheten på ljust problem innebär vanligt vis att användare inte vill vänta på att replikeringen ska ske innan den genomför en transaktion, så det finns risk för data förlust när du gör oplanerad redundans.

Databaser i tjänst nivåerna Premium och Business Critical gör det redan [något liknande](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) för synkroniseringen av en tillgänglighets grupp. Databaser med lägre tjänst nivåer ger redundans genom lagring med hjälp av en [annan men likvärdig mekanism](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Det finns logik som skyddar mot ett enskilt dator haveri.  Funktionen aktiv geo-replikering ger dig möjlighet att skydda mot katastrofer där en hel region förstörs.

Azure-tillgänglighetszoner är en uppspelning av problemet med hög tillgänglighet.  Det försöker skydda mot avbrott i en enskild Data Center-byggnad inom en enda region.  Det vill därför skydda mot förlust av ström eller nätverk till en byggnad. I SQL Azure fungerar detta genom att placera de olika replikerna i olika tillgänglighets zoner (olika byggnader) och fungerar på andra sätt som tidigare.

Azures branschledande service nivå avtal [(SLA)](https://azure.microsoft.com/support/legal/sla/)med 99,99% tillgänglighet, som drivs av ett globalt nätverk av Microsoft-hanterade data Center, hjälper till att hålla appen igång 24/7. Azure-plattformen hanterar fullständigt alla databaser och garanterar ingen data förlust och hög procent andel data tillgänglighet. Azure hanterar automatiskt uppdatering, säkerhetskopiering, replikering, felidentifiering, fel med underliggande maskinvara, programvara eller nätverk, distribuering av felkorrigeringar, redundans, databasuppgraderingar och andra underhållsåtgärder. Standardtillgänglighet uppnås genom en uppdelning av beräknings och lagringslager. Premium-tillgänglighet uppnås genom att integrera beräkning och lagring på en enda nod för prestanda och sedan implementera teknik som liknar Always on-tillgänglighetsgrupper under försättsblad. En fullständig beskrivning av funktionerna för hög tillgänglighet i Azure SQL Database finns [SQL Database tillgänglighet](sql-database-high-availability.md). SQL Database tillhandahåller dessutom inbyggda funktioner för [kontinuitet för företag och global skalbarhet](sql-database-business-continuity.md), inklusive:

- **[Automatisk säkerhets kopiering](sql-database-automated-backups.md)** :

  SQL Database automatiskt utför fullständiga, differentiella och transaktions logg säkerhets kopior av Azure SQL-databaser så att du kan återställa till vilken tidpunkt som helst. För enskilda databaser och databaser i pooler kan du konfigurera SQL Database att lagra fullständiga säkerhets kopior av databasen i Azure Storage för långsiktig kvarhållning av säkerhets kopior. För hanterade instanser kan du även utföra säkerhets kopieringar för långsiktig kvarhållning av säkerhets kopior.

- **[Återställning av](sql-database-recovery-using-backups.md)** tidpunkt:

  Alla SQL Database distributions alternativ stöder återställning till vilken tidpunkt som helst inom den automatiska kvarhållningsperioden för säkerhets kopiering för alla Azure SQL-databaser.
- **[Aktiv geo-replikering](sql-database-active-geo-replication.md)** :

  Med enkel databas och databaser i pooler kan du konfigurera upp till fyra läsbara sekundära databaser i antingen samma eller globalt distribuerade Azure-datacenter.  Till exempel om du har ett SaaS-program med en katalog-databas som har ett stort antal samtidiga skrivskyddade transaktioner, använder aktiv geo-replikering för att aktivera global skrivskyddsskala och tar bort flaskhalsar på primärorsaken som beror på skrivskyddade arbetsbelastningar. För hanterade instanser använder du grupper för automatisk redundans.
- **[Grupper för automatisk redundans](sql-database-auto-failover-group.md)** :

  Med alla SQL Database distributions alternativ kan du använda grupper för växling vid fel för att aktivera hög tillgänglighet och belastnings utjämning i global skala, inklusive transparent geo-replikering och redundans av stora mängder databaser, elastiska pooler och hanterade instanser. Med failover-grupper kan du skapa globalt distribuerade SaaS-program med minimal administrations kapacitet och samtidigt dirigera all komplex övervakning, Routning och redundanskluster till SQL Database.
- **[Zoner-redundanta databaser](sql-database-high-availability.md)** :

  Med SQL Database kan du etablera Premium-eller affärs kritiska databaser eller elastiska pooler i flera tillgänglighets zoner. Eftersom dessa databaser och elastiska pooler har flera redundanta repliker för hög tillgänglighet får du mer elasticitet om du placerar replikerna i flera tillgänglighetszoner. Du kan även återställa automatiskt från datacenters skalningsfel utan dataförlust.

## <a name="built-in-intelligence"></a>Inbyggd intelligens

Med SQL-databas får du inbyggd intelligens som hjälper dig att avsevärt minska kostnaderna för att köra och hantera databaser och maximera både prestanda och säkerhet för programmet. Miljontals kundarbetsflöden körs dygnet runt, SQL Database samlar in och bearbetar en omfattande mängd telemetridata, samtidigt som den helt respekterar kundens integritet i bakgrunden. Olika algoritmer utvärderar kontinuerligt telemetridata så att tjänsten kan lära sig och anpassa dem med ditt program. Med hjälp av den här analysen är tjänsten uppdaterad med prestanda förbättrings rekommendationer som är skräddarsydda för din speciella arbets belastning.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisk prestandaövervakning och justering

SQL Database ger detaljerad inblick i frågor som du behöver övervaka. SQL-databas lär sig om din databas och du kan anpassa ditt databasschema för din arbetsbelastning. SQL-databasen innehåller [rekommendationer för prestandajustering](sql-database-advisor.md) där du kan granska justeringsåtgärder och använda dem.

Att ständigt övervaka en databas är dock en svår och omständlig uppgift, särskilt när du hanterar många databaser. [Intelligent Insights](sql-database-intelligent-insights.md) gör det här åt dig genom att automatiskt övervaka prestandan för SQL Database med skalning och informerar dig vid problem med prestandaförsämring. Det identifierar rotorsaken till problemet och ger rekommendationer om prestandaförbättring när så är möjligt.

Det kan vara svårt att hantera ett stort antal databaser effektivt, även om du har alla verktyg och rapporter i SQL Database och Azure-portalen. I stället för att övervaka och justera databasen manuellt, kan du delegera vissa åtgärder som övervakar och justerar till SQL Database med hjälp av [automatisk justering](sql-database-automatic-tuning.md). SQL Database använder automatiskt rekommendationer, tester och verifierar var och en av dess justerings åtgärder för att säkerställa att prestandan förbättras. På så sätt kan SQL Database automatiskt anpassas efter din arbets belastning på ett kontrollerat och säkert sätt. Automatisk justering innebär att databasens prestanda övervakas och jämförs noggrant före och efter varje prestandajusteringsåtgärd. Om detta inte förbättrar prestandan återställs justeringsåtgärden.

Idag förlitar sig många av våra samarbetspartners som använder [SaaS appar för flera appar](sql-database-design-patterns-multi-tenancy-saas-applications.md) utöver SQL-databasen på automatisk prestandajustering för att säkerställa att deras program alltid hade stabil och pålitlig prestanda. För dessa minskar funktionen risken för en prestandaincident mitt i natten. Eftersom en del av deras kundbas också använder SQL Server, använder de dessutom samma indexeringsrekommendationer som tillhandahålls av SQL Database för sina SQL Server-kunder.

Det finns två automatiska justeringsaspekter som är [tillgängliga i SQL Database](sql-database-automatic-tuning.md):

- **Automatisk index hantering**: Identifierar index som ska läggas till i din databas och index som ska tas bort.
- **Automatisk plan korrigering**: Identifierar problematiska planer och åtgärdar prestanda problem i SQL-planen (kommer snart, redan tillgängligt i SQL Server 2017).

### <a name="adaptive-query-processing"></a>Anpassningsbar frågebearbetning

Vi lägger också till funktionen för [anpassningsbar frågebearbetning](/sql/relational-databases/performance/intelligent-query-processing) i SQL Database-familjen, inklusive överlagrad körning för tabellvärdesfunktioner med flera instruktioner, minnesfeedback i batchläge och anpassningsbara kopplingar i batchläge. De olika funktionerna med anpassningsbar frågebearbetning tillämpar liknande ”Läs och anpassa”-teknik, vilket bidrar till förmågan att hantera prestandaproblem som berör historiskt olösbara optimeringsproblem.

## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad

SQL-databasen innehåller en uppsättning [inbyggda funktioner för säkerhet och efterlevnad](sql-database-security-overview.md) för att uppfylla olika krav för säkerhet och regelefterlevnad i programmet.

> [!IMPORTANT]
> Azure SQL Database (alla distributions alternativ) har certifierats mot ett antal efterlevnads standarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den mest aktuella listan med SQL Database certifierings certifieringar.

### <a name="advance-threat-protection"></a>Advanced Threat Protection

Avancerad data säkerhet är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Det innefattar funktioner för att identifiera och klassificera känsliga data, hantera säkerhetsrisker i databasen och identifiera avvikande aktiviteter som kan indikera ett hot mot databasen. Det ger en samlad plats för aktivering och hantering av dessa funktioner.

- [Klassificering av data identifierings &](sql-database-data-discovery-and-classification.md):

  Den här funktionen (för närvarande i för hands version) innehåller funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera och märka & skydda känsliga data i dina databaser. Det kan användas för att ge insyn i tillståndet på din databasklassificering och för att spåra åtkomst till känsliga data i och utanför databasen.
- [Sårbarhets bedömning](sql-vulnerability-assessment.md):

  Den här tjänsten kan upptäcka, spåra och hjälpa dig att åtgärda eventuella sårbarheter i databasen. Den ger inblick i dina säkerhetstillstånd och inkluderar lämpliga åtgärder för att lösa säkerhetsproblem och förbättra databasens skydd.
- [Hot identifiering](sql-database-threat-detection.md):

  Den här funktionen identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databasen. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhetsaviseringar om potentiella säkerhetsproblem, SQL-inmatningsattacker samt avvikande åtkomstmönster i databasen. Aviseringar om hot identifiering ger information om den misstänkta aktiviteten och rekommenderar åtgärder för att undersöka och minimera hotet.

### <a name="auditing-for-compliance-and-security"></a>Granskning för efterlevnad och säkerhet

[Granskning](sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg i ditt Azure Storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

### <a name="data-encryption"></a>Datakryptering

SQL Database skyddar dina data genom att tillhandahålla kryptering för data i rörelse med [säkerhet på transport nivå](https://support.microsoft.com/kb/3135244), för data i vila med [transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)och för data som används med [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med SQL Database kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory-integrering](sql-database-aad-authentication.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [multifaktorautentisering](sql-database-ssms-mfa-authentication.md) (MFA) för att öka säkerheten för data och program med stöd för en process med enkel inloggning.

### <a name="compliance-certification"></a>Efterlevnadscertifiering

SQL Database granskas regelbundet och har certifierats mot flera efterlevnadsstandarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den mest aktuella listan med SQL Database certifierings certifieringar.

## <a name="easy-to-use-tools"></a>Lättanvända verktyg

SQL Database gör byggandet och underhållet av appar enklare och mer produktivt. Med SQL Database kan du fokusera på det du gör väst: bygga bra appar. Du kan hantera och utveckla SQL Database med verktyg och kunskaper som du redan har.

- **[Azure Portal](https://portal.azure.com/)** :

  Ett webbaserat program för att hantera alla Azure-tjänster
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)** :

  Ett kostnads fritt, nedladdnings Bart klient program för att hantera alla SQL-infrastrukturer, från SQL Server till SQL Database
- **[SQL Server data tools i Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)** :

  Ett kostnads fritt, nedladdnings Bart klient program för att utveckla SQL Server Relations databaser, Azure SQL-databaser, Integration Services-paket, Analysis Services data modeller och repor ting Services-rapporter.
- **[Visual Studio-kod](https://code.visualstudio.com/docs)** :

  En kostnads fri, nedladdnings bar kod redigerare med öppen källkod för Windows, macOS och Linux som stöder tillägg, inklusive [MSSQL-tillägget](https://aka.ms/mssql-marketplace) för frågor Microsoft SQL Server, Azure SQL Database och SQL Data Warehouse.

Skapa program med Python, Java, Node.js, PHP, Ruby och .NET på MacOS, Linux och Windows har stöd för SQL-databas. SQL Database stöder samma [anslutningsbibliotek](sql-database-libraries.md) som SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions-faq"></a>Vanliga frågor och svar om SQL Database

### <a name="what-is-the-current-version-of-sql-database"></a>Vad är den aktuella versionen av SQL Database

Den aktuella versionen av SQL Database är V12. Version V11 har dragits tillbaka.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Kan jag styra när uppdaterings stopp inträffar

Nej. Effekten av korrigeringen är vanligt vis inte märkbart om du [använder logik för omprövning](sql-database-develop-overview.md#resiliency) i din app. Mer information om hur du förbereder för planerade underhålls händelser på din Azure SQL-databas finns i [Planera för underhålls händelser i Azure i Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid-förmån frågor

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Finns det dubbla användnings rättigheter med Azure Hybrid-förmån för SQL Server

Du har 180 dagar med dubbla användnings rättigheter för licensen för att säkerställa att migreringen körs sömlöst. Efter den 180-dagars perioden kan SQL Server-licensen endast användas i molnet i SQL Database och har inte dubbla användnings rättigheter lokalt och i molnet.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hur skiljer sig Azure Hybrid-förmån för SQL Server från License Mobility

Idag erbjuder vi License Mobility-förmåner för att SQL Server kunder med Software Assurance som gör det möjligt att omtilldela licenser till delade servrar från tredje part. Den här förmånen kan användas i Azure IaaS och AWS EC2.
Azure Hybrid-förmån för SQL Server skiljer sig från licens mobilitet i två viktiga områden:

- Det ger ekonomiska fördelar för att flytta hög virtualiserade arbets belastningar till Azure. SQL EE-kunder kan få 4 kärnor i Azure i Generell användning SKU för varje kärna de äger lokalt för mycket virtualiserade program. License Mobility tillåter inte några särskilda kostnads besparingar för att flytta virtualiserade arbets belastningar till molnet.
- Det tillhandahåller ett PaaS-mål på Azure (SQL Database Hanterad instans) som är mycket kompatibel med SQL Server lokalt

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Vilka är de olika rättigheterna för Azure Hybrid-förmån för SQL Server

SQL Database kunder har följande rättigheter som är kopplade till Azure Hybrid-förmån för SQL Server:

|Licens utrymme|Vad får Azure Hybrid-förmån för SQL Server dig?|
|---|---|
|SQL Server Enterprise Edition Core-kunder med SA|<li>Kan betala bas priset antingen Generell användning eller Affärskritisk SKU</li><br><li>1 kärna lokalt = 4 kärnor i Generell användning SKU</li><br><li>1 kärna lokalt = 1 kärna i Affärskritisk SKU</li>|
|SQL Server Standard Edition Core-kunder med SA|<li>Kan betala bas taxan endast för Generell användning SKU</li><br><li>1 kärna lokalt = 1 kärna i Generell användning SKU</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Tala med teknikteamet för SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Ställ frågor om databas administration
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Ställ frågor om utveckling
- [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Ställ tekniska frågor
- [Feedback](https://aka.ms/sqlfeedback): Rapportera buggar och begär ande funktion
- [Reddit](https://www.reddit.com/r/SQLServer/): Diskutera SQL Server

## <a name="next-steps"></a>Nästa steg

- Se [Prissidan](https://azure.microsoft.com/pricing/details/sql-database/) för en kostnadsjämförelse och kostnadsberäknare för enskilda databaser och elastiska pooler.
- Läs följande snabbstarter innan du börjar:

  - [Skapa en SQL-databas i Azure Portal](sql-database-single-database-get-started.md)  
  - [Skapa en SQL-databas med Azure CLI](sql-database-get-started-cli.md)
  - [Skapa en SQL Database med PowerShell](sql-database-get-started-powershell.md)

- En uppsättning Azure CLI- och PowerShell-exempel finns här:
  - [Azure CLI-exempel för SQL Database](sql-database-cli-samples.md)
  - [Azure PowerShell-exempel för SQL Database](sql-database-powershell-samples.md)

 - Information om nya funktioner när de meddelas finns i 
   - **[Azure-översikt för SQL Database](https://azure.microsoft.com/roadmap/?category=databases)** – en plats där du kan ta reda på vad som är nytt och vad som kommer härnäst.
  - **[Azure SQL Database blogg](https://azure.microsoft.com/blog/topics/database)** – en plats där SQL Server produkt Teams medlemmar bloggar om SQL Database nyheter och funktioner.

