---
title: Vad är tjänsten Azure SQL Database? | Microsoft Docs
description: 'Få en introduktion till SQL Database: teknisk information och funktioner i Microsofts relationella databashanteringssystem (RDBMS) i molnet.'
keywords: introduktion till sql, sql-introduktion, vad är sql database
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.date: 03/07/2018
ms.workload: Active
ms.author: carlrab
ms.reviewer: carlrab
ms.openlocfilehash: a1c4426acec8e9e006c2349b886297f45cc8ecb2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="what-is-the-azure-sql-database-service"></a>Vad är tjänsten Azure SQL Database? 

SQL-databasen är en hanterad, allmän relationsdatabastjänst i Microsoft Azure som har stöd för strukturer som relationsdata, JSON, spatial och XML. SQL Database erbjuder hanterade [enkla SQL-databaser](sql-database-servers-databases.md), hanterade SQL-databaser i en [elastisk pool](sql-database-elastic-pool.md) och hanterade SQL-instanser – som kallas [SQL Database Managed Instance](sql-database-managed-instance.md) (i allmänt tillgänglig förhandsversion). Ger [dynamiskt skalbar prestanda](sql-database-service-tiers.md) och innehåller alternativ som [kolumnlagringsindex](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) för extremanalys och rapporter, samt [minnesintern OLTP](sql-database-in-memory.md) för extrem transaktionell bearbetning. Microsoft hanterar alla korrigeringar och uppdateringar av SQL-kodbasen sömlöst och avlägsnar all hantering av underliggande den infrastrukturen. 

SQL-databas delar sin kodbas med [Microsoft SQL Server-databasmotorn](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). Med Microsofts moln-först-strategi släpps de senaste funktionerna för SQL Server först till SQL-databasen och sedan till SQL-servern. Den här metoden ger de senaste funktionerna för SQL Server utan merkostnader för uppdatering eller uppgradering och med nya funktioner som testas över miljontals databaser. Mer information om nya funktioner efter hand som de tillkännages finns i:

- **[Azure-produktöversikt för SQL Database](https://azure.microsoft.com/roadmap/?category=databases)** Här reda på vad som är nytt och vad som är på gång. 
- **[Azure SQL Database-blogg](https://azure.microsoft.com/blog/topics/database)**: en plats där SQL Server-produktteamet bloggar om nyheter och funktioner om SQL Database. 

> [!IMPORTANT]
> Funktionsskillnaderna mellan SQL Database och SQL Server finns i [SQL-funktioner](sql-database-features.md). 

SQL Database levererar förutsägbar prestanda på flera servicenivåer som ger dynamisk skalbarhet utan driftstopp, inbyggd intelligent optimering, global skalbarhet och tillgänglighet samt alternativ för avancerad säkerhet – allt detta med nästan obefintlig administration. Dessa funktioner gör att du kan fokusera på snabb apputveckling och att accelerera din tid till marknaden, istället för att ägna värdefull tid och resurser åt att hantera virtuella datorer och infrastruktur. SQL Database-tjänsten används för närvarande i 38 datacenter runtom i världen och många fler datacenter ansluter sig hela tiden, vilket innebär att du kan köra din databas från ett datacenter nära dig.

> [VIKTIGT!] SQL Database Managed Instance finns för närvarande i förhandsversion och är endast tillgänglig på en enda servicenivå. Mer information finns i [SQL Database Managed Instance](sql-database-managed-instance.md).
>

## <a name="scalable-performance-and-pools"></a>Skalbar prestanda och pooler

Med SQL-databas måste alla databaser vara isolerade från varandra och portabla, var och en med sin egen [tjänstnivå](sql-database-service-tiers.md) med en garanterad prestandanivå. SQL-databasen innehåller olika prestandanivåer för olika behov och gör att databaserna är poolade för att maximera användningen av resurser och spara pengar.

Med SQL Database Managed Instance är varje instans isolerad från andra instanser med garanterade resurser. Mer information finns i [SQL Database Managed Instance](sql-database-managed-instance.md) 

### <a name="adjust-performance-and-scale-without-downtime"></a>Justera prestanda och skalning utan avbrott

I SQL Database finns tre tjänstnivåer som ger stöd för allt från lätta till tunga arbetsbelastningar: Basic, Standard och Premium. Du kan skapa din första app på en liten, enkel databas för en låg månadskostnad och sedan ändra dess tjänstnivå manuellt eller programmässigt när som helst för att uppfylla behoven i din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

   ![skalning](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

SQL Database Managed Instance finns i förhandsversion och erbjuder en enda servicenivå. Mer information finns i [SQL Database Managed Instance](sql-database-managed-instance.md)

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastiska pooler som maximerar resursutnyttjandet

För många företag och appar räcker det att kunna skapa enkla databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. [Elastiska pooler](sql-database-elastic-pool.md) är utformade för att lösa detta problem. Konceptet är enkelt. Du allokerar prestandaresurser till en pool snarare än till en individuell databas, och betalar för de samlade prestandaresurserna för poolen istället för en enskild databas prestanda. 

   ![elastiska pooler](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Med elastiska pooler behöver du inte fokusera på att reglera databasprestanda upp och ner när behovet av resurser varierar. Databaserna i poolen förbrukar den elastiska poolens prestandaresurser efter behov. Databaser i poolen förbrukar, men överskrider inte begränsningarna i poolen. Dina kostnader förblir förutsägbara även om den individuella databasanvändningen inte är det. Dessutom kan du [lägga till och ta bort databaser i poolen](sql-database-elastic-pool-manage-portal.md) och därmed skala din app från några få databaser till tusentals, inom en budget som du själv styr över. Du också styra de minimalt och maximalt tillgängliga resurser som är tillgängliga för databaser i poolen, för att säkerställa att ingen databas i poolen använder alla poolresurser och att varje databas i poolen har garanterade resurser. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!IMPORTANT]
> SQL Database Managed Instance stöder inte elastiska pooler.

### <a name="blend-single-databases-with-pooled-databases"></a>Blanda enskilda databaser med databaser i en pool

Vilken väg du än väljer – enskilda databaser eller elastiska pooler – är du inte låst. Du kan blanda enskilda databaser med elastiska pooler och snabbt och enkelt ändra tjänstnivåerna för enskilda databaser och elastiska pooler efter behov. Med den kraft och räckvidd som Azure har, kan du dessutom blanda andra Azure-tjänster med SQL Database, allt för att möta designbehoven för just din unika app, samt styra kostnader och upptäcka nya affärsmöjligheter.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Omfattande övervakning och aviseringsfunktioner

Men hur kan man jämföra den relativa prestandan för enskilda databaser och elastiska pooler? Hur vet man rätt värden när man reglerar upp eller ner? Du använder den [inbyggda prestandaövervakningen](sql-database-performance.md) och [aviserings](sql-database-insights-alerts-portal.md)verktyg, i kombination med prestandaklassificeringarna baserade på [databastransaktionsenheter (DTU:er) för enskilda databaser och elastiska DTU:er (eDTU:er) för elastiska pooler](sql-database-what-is-a-dtu.md). Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner baserat på dina aktuella eller projekterade prestandakrav. Se [SQL Database, alternativ och prestanda: Förstå vad varje tjänstnivå erbjuder](sql-database-service-tiers.md) för mer information.

Dessutom kan SQL-databasen [skapa mått och diagnostikloggar](sql-database-metrics-diag-logging.md) för lättare övervakning. Du kan konfigurera SQL-databasen för att lagra resursanvändning, personal och sessioner och anslutning till en av dessa Azure-resurser:

- **Azure Storage**: För arkivering av stora mängder telemetri till ett lågt pris
- **Azure Event Hub**: För integrering av telemetri i SQL-databasen med anpassad övervakningslösning eller heta pipelines
- **Azure Log Analytics**: För inbyggda övervakningslösningar med rapporteringer, avisering och problemlösningskapacitet

    ![Arkitektur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Kapacitet för tillgänglighet

Azures branschledande serviceavtal [(SLA)](http://azure.microsoft.com/support/legal/sla/) med 99,99 % tillgänglighet, drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång 24/7. SQL Database tillhandahåller dessutom inbyggda funktioner för [kontinuitet för företag och global skalbarhet](sql-database-business-continuity.md), inklusive:

- **[Automatisk säkerhetskopiering](sql-database-automated-backups.md)**: SQL-databasen utför automatiskt fullständiga och differentiella säkerhetskopieringar samt säkerhetskopiering av transaktionsloggen.
- **[Återställning vid tidpunkt](sql-database-recovery-using-backups.md)**: SQL-databasen har stöd för återställning till en valfri tidpunkt inom den automatiska kvarhållningsperioden för säkerhetskopiering.
- **[Aktiv geo-replikering](sql-database-geo-replication-overview.md)**: Med SQL-databasen kan du konfigurera upp till fyra läsbara sekundära databaser i antingen samma eller globalt distribuerade Azure-datacenter.  Till exempel om du har ett SaaS-program med en katalog-databas som har ett stort antal samtidiga skrivskyddade transaktioner, använder aktiv geo-replikering för att aktivera global skrivskyddsskala och tar bort flaskhalsar på primärorsaken som beror på skrivskyddade arbetsbelastningar. 
- **[Redundansgrupper](sql-database-geo-replication-overview.md)**: SQL-databasen kan du aktivera hög tillgänglighet och belastningsutjämning på global nivå, inklusive transparent geo-replikering och redundans för stora mängder databaser och elastiska pooler. Redundansgrupper och aktiv geo-replikering kan du skapa globalt distribuerade SaaS-program med minimala administrationsomkostnader, vilket lämnar all komplex övervakning, rutt, och felstyrning till SQL-databasen.
- **[Zonredundanta databaser](sql-database-high-availability.md)**: Med SQL Database kan du etablera Premium-databaser eller elastiska Premium-pooler i flera tillgänglighetszoner. Eftersom Premium-databaser och elastiska Premium-pooler har flera redundanta repliker för hög tillgänglighet får du mer elasticitet om du placerar replikerna i flera tillgänglighetszoner. Du kan även återställa automatiskt från datacenters skalningsfel utan dataförlust. Den här funktionen är för närvarande en förhandsversion. 

## <a name="built-in-intelligence"></a>Inbyggd intelligens

Med SQL-databas får du inbyggd intelligens som hjälper dig att avsevärt minska kostnaderna för att köra och hantera databaser och maximera både prestanda och säkerhet för programmet. Miljontals kundarbetsflöden körs dygnet runt, SQL Database samlar in och bearbetar en omfattande mängd telemetridata, samtidigt som den helt respekterar kundens integritet i bakgrunden. Olika algoritmer utvärderar kontinuerligt telemetridata så att tjänsten kan lära sig och anpassa dem med ditt program. Baserat på den här analysen skapar tjänsten rekommendationer som ökar prestandan på ett sätt som är skräddarsytt för din arbetsbelastning. 

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisk prestandaövervakning och justering

SQL Database ger detaljerad inblick i frågor som du behöver övervaka. SQL-databas lär sig om din databas och du kan anpassa ditt databasschema för din arbetsbelastning. SQL-databasen innehåller [rekommendationer för prestandajustering](sql-database-advisor.md) där du kan granska justeringsåtgärder och använda dem. 

Det kan vara svårt att konstant övervaka databasen, särskilt när vi hanterar flera databaser. [Intelligent Insights](sql-database-intelligent-insights.md) gör det här åt dig genom att automatiskt övervaka prestandan för SQL Database med skalning och informerar dig vid problem med prestandaförsämring. Det identifierar rotorsaken till problemet och ger rekommendationer om prestandaförbättring när så är möjligt.

Det kan vara svårt att hantera ett stort antal databaser effektivt, även om du har alla verktyg och rapporter i SQL Database och Azure-portalen. I stället för att övervaka och justera databasen manuellt, kan du delegera vissa åtgärder som övervakar och justerar till SQL Database med hjälp av [automatisk justering](sql-database-automatic-tuning.md). SQL Database tillämpar automatiskt rekommendationer, tester och verifierar var och en av prestandajusteringsåtgärderna för att kontrollera att prestandan ständigt förbättras. På så sätt kan SQL-databasen automatiskt anpassas efter din arbetsbelastning på ett kontrollerat och säkert sätt. Automatisk justering innebär att databasens prestanda övervakas och jämförs noggrant före och efter varje prestandajusteringsåtgärd. Om detta inte förbättrar prestandan återställs justeringsåtgärden.

Idag förlitar sig många av våra samarbetspartners som använder [SaaS appar för flera appar](sql-database-design-patterns-multi-tenancy-saas-applications.md) utöver SQL-databasen på automatisk prestandajustering för att säkerställa att deras program alltid hade stabil och pålitlig prestanda. För dessa minskar funktionen risken för en prestandaincident mitt i natten. Eftersom en del av deras kundbas också använder SQL Server, använder de dessutom samma indexeringsrekommendationer som tillhandahålls av SQL Database för sina SQL Server-kunder.

Det finns två automatiska justeringsaspekter som är [tillgängliga i SQL Database](sql-database-automatic-tuning.md):

- **Automatisk indexhantering**: Identifierar index som bör läggas till i din databas och index som ska tas bort.
- **Plan för automatisk korrigering**: Identifierar problematiska planer och åtgärdar prestandaproblem i SQL-planen (kommer snart, finns redan i SQL Server 2017).

### <a name="adaptive-query-processing"></a>Anpassningsbar frågebearbetning

Vi lägger också till funktionen för [anpassningsbar frågebearbetning](/sql/relational-databases/performance/adaptive-query-processing) i SQL Database-familjen, inklusive överlagrad körning för tabellvärdesfunktioner med flera instruktioner, minnesfeedback i batchläge och anpassningsbara kopplingar i batchläge. De olika funktionerna med anpassningsbar frågebearbetning tillämpar liknande ”Läs och anpassa”-teknik, vilket bidrar till förmågan att hantera prestandaproblem som berör historiskt olösbara optimeringsproblem.

### <a name="intelligent-threat-detection"></a>Intelligent hotidentifiering

 [SQL-Hotidentifiering](sql-database-threat-detection.md) utnyttjar [SQL Database auditing](sql-database-auditing.md) för att ständigt övervaka Azure SQL-databaser och förhindra obehöriga försök att komma åt känsliga data. SQL-hotidentifiering ger ett nytt lager av säkerhet som ger kunder möjlighet att identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användare får aviseringar om misstänkta databasaktiviteter, potentiella svagheter, SQL-filinmatningsattacker och avvikande mönster i databasåtkomst. SQL-hotidentifieringsaviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minska risken. Användare kan utforska misstänkta händelser för att avgöra om händelsen orsakades av ett försök att komma åt, tränga in i eller utnyttja data i databasen. Hotidentifiering gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhetsexpert eller hantera avancerade säkerhetsövervakningssystem.

## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad

SQL-databasen innehåller en uppsättning [inbyggda funktioner för säkerhet och efterlevnad](sql-database-security-overview.md) för att uppfylla olika krav för säkerhet och regelefterlevnad i programmet. 

### <a name="auditing-for-compliance-and-security"></a>Granskning för efterlevnad och säkerhet

[SQL-databasgranskning](sql-database-auditing.md) spårar databashändelser och skriver händelserna i en granskningslogg i ditt Azure Storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

### <a name="data-encryption-at-rest"></a>Datakryptering i vila

SQL Database [transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) skyddar mot hot från skadlig aktivitet genom att utföra kryptering och dekryptering av databas i realtid, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet. Från och med kan 2017 skyddas alla nyligen skapade Azure SQL-databaser automatiskt med transparent datakryptering (TDE). TDE är en SQL-beprövad kryptering i vila-teknik som krävs av många efterlevnadsstandarder för att skydda mot stöld av lagringsmedier. Kunder kan hantera TDE-krypteringsnycklar och andra hemligheter säkert och i efterlevnad av krav med Azure Key Vault.

### <a name="data-encryption-in-motion"></a>Datakryptering i rörelse

SQL-databasen är det databassystemet som erbjuder skydd av känsliga data i rörelse, i vila och under frågebearbetning med [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted är först i branschen med att erbjuda oslagbar datasäkerhet mot överträdelser, inklusive stöld av viktiga data. Till exempel, med Always Encrypted lagras kunden kreditkortsnummer alltid i databasen i krypterad form, även under frågebearbetning, vilket möjliggör dekryptering vid behov av behörig personal eller program som behöver bearbeta datan.

### <a name="dynamic-data-masking"></a>Dynamisk datamaskning

[SQL Database dynamisk datamaskning](sql-database-dynamic-data-masking-get-started.md) begränsar exponering av känsliga data genom att maskera den för icke-privilegierade användare. Dynamisk datamaskning förhindrar obehörig åtkomst till känsliga data genom att låta kunder ange hur mycket av känsliga data som avslöja med minimal påverkan på programnivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.

### <a name="row-level-security"></a>Säkerhet på radnivå

[Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) ger kunder möjlighet att styra åtkomsten till rader i en databastabell baserat på egenskaperna för användaren som kör en fråga (t.ex. grupmedlemskap eller körningskontext). Säkerheten på radnivå (RLS) förenklar design och kodning av säkerheten i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Därmed får medarbetare endast tillgång till de datarader som är relevanta för deras avdelning, eller kunder får endast dataåtkomst till data som berör deras företag.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med SQL Database kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory-integrering](sql-database-aad-authentication.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [multifaktorautentisering](sql-database-ssms-mfa-authentication.md) (MFA) för att öka säkerheten för data och program med stöd för en process med enkel inloggning.

### <a name="compliance-certification"></a>Efterlevnadscertifiering

SQL Database granskas regelbundet och har certifierats mot flera efterlevnadsstandarder. Mer information finns i [Microsoft Azure säkerhetscenter](https://azure.microsoft.com/support/trust-center/), där du hittar den senaste listan med [SQL Database-kompatibilitetscertifieringar](https://azure.microsoft.com/support/trust-center/services/).

## <a name="easy-to-use-tools"></a>Lättanvända verktyg

SQL Database gör byggandet och underhållet av appar enklare och mer produktivt. Med SQL Database kan du fokusera på det du gör väst: bygga bra appar. Du kan hantera och utveckla SQL Database med verktyg och kunskaper som du redan har.

- **[Azure-portalen](https://portal.azure.com/)**: ett webbaserat program för att hantera alla Azure-tjänster 
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)**: ett kostnadsfritt, nedladdningsbart klientprogram för att hantera all SQL-infrastruktur från SQL Server till SQL Database
- **[SQL Server Data Tools i Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)**: ett kostnadsfritt, nedladdningsbart klientprogram för att utveckla relationsdatabaser i SQL Server, Azure SQL-databaser, Integration Services-paket, datamodeller för Analysis Services och Reporting Services-rapporter.
- **[Visual Studio Code](https://code.visualstudio.com/docs)** är en kostnadsfri, nedladdningsbar kodredigerare med öppen källkod för Linux, macOS och Windows som stöder tillägg, inklusive [mssql-tillägget](https://aka.ms/mssql-marketplace) för frågor till Microsoft SQL Server, Azure SQL Database och SQL Data Warehouse.

Skapa program med Python, Java, Node.js, PHP, Ruby och .NET på MacOS, Linux och Windows har stöd för SQL-databas. SQL Database stöder samma [anslutningsbibliotek](sql-database-libraries.md) som SQL Server.

## <a name="engage-with-the-sql-server-engineering-team"></a>Tala med teknikteamet för SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Ställ frågor om databasadministration
- [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-server): Ställ frågor om utveckling
- [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Ställ tekniska frågor
- [Feedback](http://aka.ms/sqlfeedback): Rapportera fel och kom med förslag
- [Reddit](https://www.reddit.com/r/SQLServer/): Diskutera SQL Server

## <a name="next-steps"></a>Nästa steg

- Se [Prissidan](https://azure.microsoft.com/pricing/details/sql-database/) för en kostnadsjämförelse och kostnadsberäknare för enskilda databaser och elastiska pooler.

- Läs följande snabbstarter innan du börjar:

  - [Skapa en SQL-databas i Azure Portal](sql-database-get-started-portal.md)  
  - [Skapa en SQL-databas med Azure CLI](sql-database-get-started-cli.md)
  - [Skapa en SQL Database med PowerShell](sql-database-get-started-powershell.md)

- En uppsättning Azure CLI- och PowerShell-exempel finns här:
  - [Azure CLI-exempel för SQL Database](sql-database-cli-samples.md)
  - [Azure PowerShell-exempel för SQL Database](sql-database-powershell-samples.md)
