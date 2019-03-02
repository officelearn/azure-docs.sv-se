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
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 29a0d0679adc51cf1493d241273e3b572a7fd57d
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214307"
---
# <a name="what-is-azure-sql-database-service"></a>Vad är Azure SQL Database-tjänsten?

SQL-databasen är en hanterad, allmän relationsdatabastjänst i Microsoft Azure som har stöd för strukturer som relationsdata, JSON, spatial och XML. SQL Database levererar dynamiskt skalbar prestanda i två olika inköpschef modeller: en vCore-baserade inköpsmodellen och en DTU-baserade inköpsmodellen. SQL Database tillhandahåller också alternativ som [kolumnlagringsindex](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) för extrem analytisk analys och rapportering, och [minnesintern OLTP](sql-database-in-memory.md) för extrem transaktionell bearbetning. Microsoft hanterar alla korrigeringar och uppdateringar av SQL-kodbasen sömlöst och avlägsnar all hantering av underliggande den infrastrukturen.

> [!NOTE]
> En ordlista i Azure SQL Database finns [SQL Database allmänna ordlista](sql-database-glossary-terms.md)

Azure SQL Database innehåller följande distributionsalternativ för en Azure SQL-databas:

- Som en [enkel databas](sql-database-single-database.md) med en egen uppsättning resurser som hanteras via SQL-databasserver. En enkel databas liknar en [inneslutna databaser](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) i SQL Server.
- En [elastisk pool](sql-database-elastic-pool.md), vilket är en uppsättning databaser med en delad uppsättning resurser som hanteras via en SQL Database-server. Enkla databaser kan flyttas in och ut från en elastisk pool.
- [Hanterad instans](sql-database-managed-instance.md), som är en samling av system- och -databaser med en delad uppsättning resurser. En hanterad instans liknar en instans av den [Microsoft SQL Server-databasmotorn](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation).

Följande bild visar dessa distributionsalternativ:

![distributionsalternativ](./media/sql-database-technical-overview/deployment-options.png)

SQL-databas delar sin kodbas med [Microsoft SQL Server-databasmotorn](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). Med Microsofts moln-först-strategi släpps de senaste funktionerna för SQL Server först till SQL-databasen och sedan till SQL-servern. Den här metoden ger de senaste funktionerna för SQL Server utan merkostnader för uppdatering eller uppgradering och med nya funktioner som testas över miljontals databaser. Mer information om nya funktioner efter hand som de tillkännages finns i:

- **[Azure-Produktöversikt för SQL-databas](https://azure.microsoft.com/roadmap/?category=databases)**:

  En plats för att ta reda på vad som är nytt och vad som kommer härnäst.

- **[Azure SQL Database-blogg](https://azure.microsoft.com/blog/topics/database)**:

  En plats där SQL Server-produktteamet bloggar om nyheter för SQL-databas och funktioner.

> [!IMPORTANT]
> Information om funktionsskillnaderna mellan SQL Database och SQL Server, samt skillnaderna mellan olika distributionsalternativ för Azure SQL Database finns i [SQL-funktioner](sql-database-features.md).

SQL Database levererar förutsägbar prestanda med flera resurstyper, tjänstnivåer och storlekar som ger dynamisk skalbarhet utan driftstopp, inbyggd intelligent optimering, global skalbarhet och tillgänglighet och avancerad säkerhet alternativ – allt med nästan obefintlig administration. Dessa funktioner gör att du kan fokusera på snabb apputveckling och att accelerera din tid till marknaden, istället för att ägna värdefull tid och resurser åt att hantera virtuella datorer och infrastruktur. SQL Database-tjänsten används för närvarande i 38 datacenter runtom i världen och många fler datacenter ansluter sig hela tiden, vilket innebär att du kan köra din databas från ett datacenter nära dig.

## <a name="scalable-performance-and-pools"></a>Skalbar prestanda och pooler

- Varje databas är isolerade från varandra och portabla, var och en med sin egen garanterad mängd resurser för beräkning, minne och lagring med en enskild databas. SQL-databasen innehåller olika resurser som beräkning, minne och lagring för olika behov – och möjligheten att dynamiskt [skala resurser för enkel databas](sql-database-single-database-scale.md) uppåt och nedåt. Den [hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md) (förhandsversion) för enkel databas kan du skala till 100 TB med snabb säkerhetskopiering och återställningsfunktioner.
- Med elastiska pooler kan du skapa nya databaser eller flytta enskilda databaser i en resurspool för att maximera användningen av resurser och spara pengar – och möjligheten att dynamiskt [skala elastisk poolresurser](sql-database-elastic-pool-scale.md) uppåt och nedåt.
- Med hanterade instanser är varje hanterad instans isolerad från andra instanser med garanterade resurser. I en hanterad instans, instansdatabaser delar en uppsättning resurser- och möjligheten att dynamiskt [skala resurser för hanterad instans](sql-database-managed-instance-resource-limits.md) uppåt och nedåt.

Du kan skapa din första app på en liten, enkel databas till en låg kostnad per månad på nivån generell användning tjänsten och sedan ändra dess tjänstnivå manuellt eller programmässigt när som helst på kritiska tjänstnivån för företag att uppfylla behoven i din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

Dynamisk skalbarhet skiljer sig från autoskalning. Med Autoskala avses när en tjänst skalar automatiskt utifrån olika kriterier, medan dynamisk skalbarhet möjliggör manuell skalning utan avbrott. En enkel databas har stöd för manuell dynamisk skalbarhet, men inte automatisk skalning. Om du vill ha en mer *automatisk* upplevelse bör du använda elastiska pooler, vilka tillåter databaser att dela resurser i en pool utifrån enskilda databasbehov. Det finns dock skript som kan hjälpa dig att automatisera skalbarhet för en enskild databas. Ett exempel finns i [Använd PowerShell för att övervaka och skala en enskild databas](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>Köpa modeller, tjänstnivåer, storlekar och lagring belopp

SQL Database erbjuder två inköpschef modeller:

- Den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) erbjuder en kombination av beräkning, minne, IO-resurser i tre tjänstnivåer för att stödja lätta till tunga arbetsbelastningar. Compute-storlekar inom varje nivå med en blandning av dessa resurser, där du kan lägga till ytterligare lagringsresurser.
- Den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) kan du välja antal virtuella kärnor, hur mycket eller minne, och mycket och snabbt lagringsutrymme. Den vCore-baserade inköpsmodellen kan du använda [Azure Hybrid-förmånen för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) att få kostnadsbesparingar. Läs mer om Azure Hybrid-förmånen [vanliga frågor och svar](#sql-database-frequently-asked-questions-faq).

  > [!IMPORTANT]
  > Den [hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md) för enskilda databaser är för närvarande i offentlig förhandsversion. Vi rekommenderar inte någon produktionsarbetsbelastning körs ännu i hyperskala databaser. Du kan inte uppdatera en storskalig databas till andra tjänstnivåer. För testning syfte rekommenderar vi du gör en kopia av den aktuella databasen och uppdatera kopian hyperskala tjänstnivån.

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastiska pooler som maximerar resursutnyttjandet

För många företag och appar räcker det att kunna skapa enkla databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. [Elastiska pooler](sql-database-elastic-pool.md) är utformade för att lösa detta problem. Konceptet är enkelt. Du allokerar prestandaresurser till en pool i stället för en individuell databas och betalar för de samlade prestandaresurserna för poolen istället för prestandan för enskilda databaser.

   ![elastiska pooler](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Med elastiska pooler behöver du inte fokusera på att reglera databasprestanda upp och ner när behovet av resurser varierar. Databaserna i poolen förbrukar den elastiska poolens prestandaresurser efter behov. Databaser i poolen förbrukar, men överskrider inte begränsningarna i poolen. Dina kostnader förblir förutsägbara även om den individuella databasanvändningen inte är det. Dessutom kan du [lägga till och ta bort databaser i poolen](sql-database-elastic-pool-manage-portal.md) och därmed skala din app från några få databaser till tusentals, inom en budget som du själv styr över. Du också styra de minimalt och maximalt tillgängliga resurser som är tillgängliga för databaser i poolen, för att säkerställa att ingen databas i poolen använder alla poolresurser och att varje databas i poolen har garanterade resurser. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Skript kan hjälpa dig med att övervaka och skala elastiska pooler. Mer information finns i [Använda PowerShell till att övervaka och skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> En hanterad instans stöder inte elastiska pooler. I stället är en hanterad instans en samling av instansdatabaser som delar resurser som hanterad instans.

### <a name="blend-single-databases-with-pooled-databases"></a>Blanda enskilda databaser med databaser i en pool

Du kan blanda enskilda databaser med elastiska pooler och snabbt och enkelt ändra tjänstnivåerna för enskilda databaser och elastiska pooler efter behov för din situation. Med den kraft och räckvidd som Azure har, kan du dessutom blanda andra Azure-tjänster med SQL Database, allt för att möta designbehoven för just din unika app, samt styra kostnader och upptäcka nya affärsmöjligheter.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Omfattande övervakning och aviseringsfunktioner

Du använder de inbyggda verktygen för [prestandaövervakning](sql-database-performance.md) och [avisering](sql-database-insights-alerts-portal.md) i kombination med prestandaklassificering. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner baserat på dina aktuella eller projekterade prestandakrav. Dessutom kan SQL-databasen [skapa mått och diagnostikloggar](sql-database-metrics-diag-logging.md) för lättare övervakning. Du kan konfigurera SQL-databasen för att lagra resursanvändning, personal och sessioner och anslutning till en av dessa Azure-resurser:

- **Azure Storage**: För arkivering av stora mängder telemetri till ett lågt pris
- **Azure Event Hub**: För att integrera telemetri för SQL-databas med din anpassade övervakningslösning eller med heta pipelines
- **Azure Monitor-loggar**: För inbyggd övervakningslösning med rapporter, aviseringar och problemlösningsfunktioner.

    ![Arkitektur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Kapacitet för tillgänglighet

I en traditionell SQL Server-miljö, skulle du vanligtvis har (minst) 2 datorer lokalt konfigurera med exakt (synkront behålla) kopior av data (med funktioner som AlwaysOn-Tillgänglighetsgrupper eller Redundansklusterinstanser) att skydda mot en fel på en enskild dator/komponent.  Detta ger hög tillgänglighet men skyddar inte mot en naturkatastrof förstöra ditt datacenter.
 
Katastrofåterställning förutsätter att en händelse av katastrof är geografiskt lokaliserade tillräckligt långt har en annan dator/uppsättning datorer med en kopia av dina data.  Du kan använda Always On-Tillgänglighetsgrupper som körs i asynkron läge i SQL Server för att hämta den här funktionen.  Hastigheten på ljus problem innebär vanligtvis att personer som inte vill vänta tills replikeringen ske som långt innan du genomför en transaktion, så det finns en risk för dataförlust när du gör oplanerade redundanser.

Databaser i den premium- och kritiska nivåer redan [göra något som är mycket lik](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) till synkronisering av en tillgänglighetsgrupp. Databaser i lägre tjänstnivåer ger redundans lagring med hjälp av en [mekanism för olika men motsvarande](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Det finns logik som skyddar mot en enskild dator-fel.  Funktionen aktiv geo-replikering ger dig möjlighet att skydda mot katastrof där en hel region förstörs.

Azure Availability Zones är en play om problemet som har hög tillgänglighet.  Försök att skydda mot längre avbrott i ett datacenter att skapa inom en enda region.  Så företaget att skydda mot förlust av kapacitets- eller nätverket till en byggnad. Detta fungerar i SQL Azure, genom att placera olika repliker i olika tillgänglighetszoner (olika byggnader effektivt) och annars fungerar som tidigare. 

I själva verket Azures branschledande ledande 99,99% tillgänglighet servicenivåavtal [(SLA)](https://azure.microsoft.com/support/legal/sla/), drivs av ett globalt nätverk av Microsoft-hanterade datacenter, gör att din app är igång 24/7. Azure-plattformen fullständigt hanterar varje databas och garanterar att inga data går förlorade och hög andel datatillgänglighet. Azure hanterar automatiskt uppdatering, säkerhetskopiering, replikering, felidentifiering, fel med underliggande maskinvara, programvara eller nätverk, distribuering av felkorrigeringar, redundans, databasuppgraderingar och andra underhållsåtgärder. Standardtillgänglighet uppnås genom en uppdelning av beräknings och lagringslager. Premium-tillgänglighet uppnås genom att integrera beräkning och lagring på en enda nod för prestanda och sedan implementera teknik som liknar ständigt aktiverade Tillgänglighetsgrupper under försättsbladen. En fullständig beskrivning av kapacitet för hög tillgänglighet i Azure SQL Database finns i [tillgänglighet för SQL Database](sql-database-high-availability.md). SQL Database tillhandahåller dessutom inbyggda funktioner för [kontinuitet för företag och global skalbarhet](sql-database-business-continuity.md), inklusive:

- **[Automatisk säkerhetskopiering](sql-database-automated-backups.md)**:

  SQL Database utför automatiskt fullständiga och differentiella säkerhetskopieringar av transaktionsloggen för Azure SQL-databaser så att du kan återställa till valfri punkt i tiden. Du kan konfigurera SQL-databas för lagring av fullständiga databassäkerhetskopieringar till Azure storage för långsiktig kvarhållning av säkerhetskopior för enskilda databaser och databaser i en pool. Du kan också utföra endast kopiering säkerhetskopior för långsiktig kvarhållning av säkerhetskopior för hanterade instanser.

- **[Point-in-time-återställningar](sql-database-recovery-using-backups.md)**:

  Alla SQL Database-distributionsalternativen stödja återställning till valfri punkt inom den automatiska kvarhållningsperioden för säkerhetskopior för alla Azure SQL-databas.
- **[Aktiv geo-replikering](sql-database-active-geo-replication.md)**:

  Enkel databas och databaser i en pool kan du konfigurera upp till fyra läsbara sekundära databaser i antingen de samma eller globalt distribuerade Azure-datacenter.  Till exempel om du har ett SaaS-program med en katalog-databas som har ett stort antal samtidiga skrivskyddade transaktioner, använder aktiv geo-replikering för att aktivera global skrivskyddsskala och tar bort flaskhalsar på primärorsaken som beror på skrivskyddade arbetsbelastningar. Använda grupper för automatisk redundans för hanterade instanser.
- **[Automatisk redundans grupper](sql-database-auto-failover-group.md)**:

  Distributionsalternativ för alla SQL-databas kan du använda grupper för växling vid fel för att aktivera hög tillgänglighet och belastningsutjämning på global nivå, inklusive transparent geo-replikering och redundans för stora mängder databaser, elastiska pooler och hanterade instanser. Redundansgrupper aktivera skapandet av globalt distribuerade SaaS-program med minimala administrationsomkostnader, vilket lämnar alla komplex övervakning, routning, och felstyrning till SQL-databas.
- **[Zonredundanta databaser](sql-database-high-availability.md)**:

  SQL Database kan du etablera premium eller kritisk business-databaser eller elastiska pooler i flera tillgänglighetszoner. Eftersom dessa databaser och elastiska pooler har flera redundanta repliker för hög tillgänglighet får du mer elasticitet om du placerar replikerna i flera tillgänglighetszoner. Du kan även återställa automatiskt från datacenters skalningsfel utan dataförlust.

## <a name="built-in-intelligence"></a>Inbyggd intelligens

Med SQL-databas får du inbyggd intelligens som hjälper dig att avsevärt minska kostnaderna för att köra och hantera databaser och maximera både prestanda och säkerhet för programmet. Miljontals kundarbetsflöden körs dygnet runt, SQL Database samlar in och bearbetar en omfattande mängd telemetridata, samtidigt som den helt respekterar kundens integritet i bakgrunden. Olika algoritmer utvärderar kontinuerligt telemetridata så att tjänsten kan lära sig och anpassa dem med ditt program. Baserat på den här analysen skapar tjänsten rekommendationer som ökar prestandan på ett sätt som är skräddarsytt för din arbetsbelastning.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisk prestandaövervakning och justering

SQL Database ger detaljerad inblick i frågor som du behöver övervaka. SQL-databas lär sig om din databas och du kan anpassa ditt databasschema för din arbetsbelastning. SQL-databasen innehåller [rekommendationer för prestandajustering](sql-database-advisor.md) där du kan granska justeringsåtgärder och använda dem.

Det kan vara svårt att konstant övervaka databasen, särskilt när vi hanterar flera databaser. [Intelligent Insights](sql-database-intelligent-insights.md) gör det här åt dig genom att automatiskt övervaka prestandan för SQL Database med skalning och informerar dig vid problem med prestandaförsämring. Det identifierar rotorsaken till problemet och ger rekommendationer om prestandaförbättring när så är möjligt.

Det kan vara svårt att hantera ett stort antal databaser effektivt, även om du har alla verktyg och rapporter i SQL Database och Azure-portalen. I stället för att övervaka och justera databasen manuellt, kan du delegera vissa åtgärder som övervakar och justerar till SQL Database med hjälp av [automatisk justering](sql-database-automatic-tuning.md). SQL Database tillämpar automatiskt rekommendationer, tester och verifierar var och en av prestandajusteringsåtgärderna för att kontrollera att prestandan ständigt förbättras. På så sätt kan SQL-databasen automatiskt anpassas efter din arbetsbelastning på ett kontrollerat och säkert sätt. Automatisk justering innebär att databasens prestanda övervakas och jämförs noggrant före och efter varje prestandajusteringsåtgärd. Om detta inte förbättrar prestandan återställs justeringsåtgärden.

Idag förlitar sig många av våra samarbetspartners som använder [SaaS appar för flera appar](sql-database-design-patterns-multi-tenancy-saas-applications.md) utöver SQL-databasen på automatisk prestandajustering för att säkerställa att deras program alltid hade stabil och pålitlig prestanda. För dessa minskar funktionen risken för en prestandaincident mitt i natten. Eftersom en del av deras kundbas också använder SQL Server, använder de dessutom samma indexeringsrekommendationer som tillhandahålls av SQL Database för sina SQL Server-kunder.

Det finns två automatiska justeringsaspekter som är [tillgängliga i SQL Database](sql-database-automatic-tuning.md):

- **Automatisk indexhantering**: Identifierar index som ska läggas till i din databas och index som ska tas bort.
- **Plan för automatisk korrigering**: Identifierar problematiska planer och åtgärdar prestandaproblem i SQL planen (kommer snart, redan finns i SQL Server 2017).

### <a name="adaptive-query-processing"></a>Anpassningsbar frågebearbetning

Vi lägger också till funktionen för [anpassningsbar frågebearbetning](/sql/relational-databases/performance/adaptive-query-processing) i SQL Database-familjen, inklusive överlagrad körning för tabellvärdesfunktioner med flera instruktioner, minnesfeedback i batchläge och anpassningsbara kopplingar i batchläge. De olika funktionerna med anpassningsbar frågebearbetning tillämpar liknande ”Läs och anpassa”-teknik, vilket bidrar till förmågan att hantera prestandaproblem som berör historiskt olösbara optimeringsproblem.

## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad

SQL-databasen innehåller en uppsättning [inbyggda funktioner för säkerhet och efterlevnad](sql-database-security-overview.md) för att uppfylla olika krav för säkerhet och regelefterlevnad i programmet.

### <a name="advance-threat-protection"></a>Advanced Threat Protection

Avancerad säkerhet är en enhetlig paketet för avancerade säkerhetsfunktioner i SQL. Det innefattar funktioner för att identifiera och klassificera känsliga data, hantera säkerhetsrisker i databasen och identifiera avvikande aktiviteter som kan indikera ett hot mot databasen. Det ger en samlad plats för aktivering och hantering av dessa funktioner.

- [Dataidentifiering och klassificering](sql-database-data-discovery-and-classification.md):

  Den här funktionen (för närvarande i förhandsversion) ger funktioner i Azure SQL Database för identifiering, klassificering, märkning och skydda känsliga data i dina databaser. Det kan användas för att ge insyn i tillståndet på din databasklassificering och för att spåra åtkomst till känsliga data i och utanför databasen.
- [Sårbarhetsbedömning](sql-vulnerability-assessment.md):

  Den här tjänsten kan upptäcka, spåra och hjälper dig att åtgärda säkerhetsrisker i databasen. Den ger inblick i dina säkerhetstillstånd och inkluderar lämpliga åtgärder för att lösa säkerhetsproblem och förbättra databasens skydd.
- [Hotidentifiering](sql-database-threat-detection.md):

  Den här funktionen identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databasen. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhetsaviseringar om potentiella säkerhetsproblem, SQL-inmatningsattacker samt avvikande åtkomstmönster i databasen. Hotidentifieringsaviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärder att undersöka och åtgärda hot.

### <a name="auditing-for-compliance-and-security"></a>Granskning för efterlevnad och säkerhet

[Granskning](sql-database-auditing.md) spårar databasen händelser och skriver dem till en granskningslogg i ditt Azure storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

### <a name="data-encryption"></a>Datakryptering

SQL Database skyddar dina data genom att tillhandahålla kryptering för data i rörelse med [transport layer security](https://support.microsoft.com/kb/3135244), för data i vila med [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), och för data som används med [ alltid krypterad](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med SQL Database kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory-integrering](sql-database-aad-authentication.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [multifaktorautentisering](sql-database-ssms-mfa-authentication.md) (MFA) för att öka säkerheten för data och program med stöd för en process med enkel inloggning.

### <a name="compliance-certification"></a>Efterlevnadscertifiering

SQL Database granskas regelbundet och har certifierats mot flera efterlevnadsstandarder. Mer information finns i [Microsoft Azure säkerhetscenter](https://azure.microsoft.com/support/trust-center/), där du hittar den senaste listan med [SQL Database-kompatibilitetscertifieringar](https://azure.microsoft.com/support/trust-center/services/).

## <a name="easy-to-use-tools"></a>Lättanvända verktyg

SQL Database gör byggandet och underhållet av appar enklare och mer produktivt. Med SQL Database kan du fokusera på det du gör väst: bygga bra appar. Du kan hantera och utveckla SQL Database med verktyg och kunskaper som du redan har.

- **[Azure-portalen](https://portal.azure.com/)**:

  Ett webbaserat program för att hantera alla Azure-tjänster
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)**:

  Ett kostnadsfritt, nedladdningsbart klientprogram för att hantera all SQL-infrastruktur från SQL Server till SQL-databas
- **[SQL Server Data Tools i Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)**:

  Ett kostnadsfritt, nedladdningsbart klientprogram för att utveckla relationsdatabaser i SQL Server, Azure SQL-databaser, Integration Services-paket, datamodeller för Analysis Services och Reporting Services-rapporter.
- **[Visual Studio Code](https://code.visualstudio.com/docs)**:

  En kostnadsfri, nedladdningsbar, öppen källkod Kodredigerare för Windows, macOS och Linux som stöder tillägg, inklusive den [mssql-tillägget](https://aka.ms/mssql-marketplace) för frågor till Microsoft SQL Server, Azure SQL Database och SQL Data Warehouse.

Skapa program med Python, Java, Node.js, PHP, Ruby och .NET på MacOS, Linux och Windows har stöd för SQL-databas. SQL Database stöder samma [anslutningsbibliotek](sql-database-libraries.md) som SQL Server.

## <a name="sql-database-frequently-asked-questions-faq"></a>Vanliga frågor (och svar FAQ) om SQL-databas

### <a name="what-is-the-current-version-of-sql-database"></a>Vad är den aktuella versionen av SQL-databas

Den aktuella versionen av SQL-databas är V12. Version V11 har tagits bort.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Jag kan styra när uppdatering driftstopp uppstår

Nej. Effekten av korrigeringar är vanligtvis inte märkbar om du [använder logik för omprövning](sql-database-develop-overview.md#resiliency) i din app. Mer information om hur du förbereder planerat underhåll utförs på Azure SQL database finns i [planering för Azure-Underhåll i Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid Benefit questions

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Finns det dubbla användningsrättigheter med Azure Hybrid-förmånen för SQL Server

Du har dubbel användningsrättigheter för att säkerställa migreringar kör smidigt 180 dagar. Efter den 180 dagar, SQL Server-licens kan bara användas i molnet i SQL-databas och inte har dubbel användning rights lokalt och i molnet.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hur skiljer sig Azure Hybrid-förmånen för SQL Server från licensmobilitet

Idag erbjuder vi license mobility-förmånerna för SQL Server-kunder med Software Assurance som tillåter ny tilldelning av sina licenser för delade-servrar från tredje part. Den här förmånen kan användas på Azure IaaS och AWS EC2.
Azure Hybrid-förmånen för SQL Server skiljer sig från licensmobilitet inom två viktiga områden:

- Det ger ekonomiska fördelar för att flytta mycket virtualiserade arbetsbelastningar till Azure. SQL EE kunder får 4 kärnor i Azure i SKU för generell användning för varje kärna de äger lokalt för mycket virtualiserade program. Licensmobilitet tillåter inte någon särskild kostnadsfördelar för att flytta virtualiserade arbetsbelastningar till molnet.
- Det ger en PaaS-mål på Azure (SQL Database Managed Instance) som är mycket kompatibelt med SQL Server on-premises

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Vilka är de specifika rättigheterna för Azure Hybrid-förmånen för SQL Server

SQL-databaskunder har följande rättigheter som är associerade med Azure Hybrid-förmånen för SQL Server:

|Licens fotavtryck|Vad kostar Azure Hybrid-förmånen för SQL Server får du?|
|---|---|
|SQL Server Enterprise Edition core kunder med SA|<li>Kan betala grundpris på generell användning eller SKU affärskritisk</li><br><li>1 kärna på lokala = 4 kärnor i SKU generell användning</li><br><li>1 kärna på lokala = 1 kärna i SKU affärskritisk</li>|
|SQL Server Standard Edition core kunder med SA|<li>Endast kan grundpris betalar SKU generell användning</li><br><li>1 kärna på lokala = 1 kärna i SKU generell användning</li>|
|||


## <a name="engage-with-the-sql-server-engineering-team"></a>Tala med teknikteamet för SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Ställ frågor om databasadministration
- [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-server): Ställ frågor om utveckling
- [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Ställa tekniska frågor
- [Feedback](https://aka.ms/sqlfeedback): Rapportera buggar och förslag
- [Reddit](https://www.reddit.com/r/SQLServer/): Diskutera SQLServer

## <a name="next-steps"></a>Nästa steg

- Se [Prissidan](https://azure.microsoft.com/pricing/details/sql-database/) för en kostnadsjämförelse och kostnadsberäknare för enskilda databaser och elastiska pooler.
- Läs följande snabbstarter innan du börjar:

  - [Skapa en SQL-databas i Azure Portal](sql-database-single-database-get-started.md)  
  - [Skapa en SQL-databas med Azure CLI](sql-database-get-started-cli.md)
  - [Skapa en SQL Database med PowerShell](sql-database-get-started-powershell.md)

- En uppsättning Azure CLI- och PowerShell-exempel finns här:
  - [Azure CLI-exempel för SQL Database](sql-database-cli-samples.md)
  - [Azure PowerShell-exempel för SQL Database](sql-database-powershell-samples.md)
