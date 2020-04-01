---
title: Hyperskala för Azure SQL Database | Microsoft-dokument
description: I den här artikeln beskrivs tjänstnivån Hyperskala i den vCore-baserade inköpsmodellen i Azure SQL Database och hur den skiljer sig från tjänstnivåerna Allmänt syfte och Affärskritisk.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: 5a9917010b7301bf70c3bebf68c35d82f4839e0f
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409049"
---
# <a name="hyperscale-service-tier"></a>Hyperskalatjänstnivå

Azure SQL Database baseras på SQL Server Database Engine-arkitektur som har justerats för molnmiljön för att säkerställa 99,99 % tillgänglighet även vid infrastrukturfel. Det finns tre arkitektoniska modeller som används i Azure SQL Database:
- Allmänt syfte/standard 
-  Hyperskala
-  Affärskritiskt/Premium

Tjänstnivån Hyperskala i Azure SQL Database är den senaste tjänstnivån i den vCore-baserade inköpsmodellen. Den här tjänstnivån är en mycket skalbar lagrings- och beräkningsprestandanivå som utnyttjar Azure-arkitekturen för att skala ut lagrings- och beräkningsresurserna för en Azure SQL-databas som ligger betydligt utanför de gränser som är tillgängliga för tjänstnivåerna Allmänt ändamål och Affärskritiskt.

> 
> [!NOTE]
> Mer information om de allmänna nivåerna för ändamål och affärskritiska tjänster i den vCore-baserade inköpsmodellen finns i [allmänna tjänstnivåer](sql-database-service-tier-general-purpose.md) och [affärskritiska](sql-database-service-tier-business-critical.md) tjänstnivåer. En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen finns i [Azure SQL Database som köper modeller och resurser](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Vilka är hyperskalefunktionerna

Tjänstnivån Hyperskala i Azure SQL Database innehåller följande ytterligare funktioner:

- Stöd för upp till 100 TB databasstorlek
- Nästan omedelbara säkerhetskopieringar av databaser (baserat på ögonblicksbilder av filer som lagras i Azure Blob-lagring) oavsett storlek utan IO-påverkan på beräkningsresurser  
- Snabb återställning av databasen (baserat på ögonblicksbilder av filer) på några minuter i stället för timmar eller dagar (inte en storlek på dataåtgärd)
- Högre övergripande prestanda på grund av högre logggenomströmning och snabbare transaktionsgenomförandetider oavsett datavolymer
- Snabb skala ut - du kan etablera en eller flera skrivskyddade noder för avlastning av din läsarbetsbelastning och för användning som hot-standbys
- Snabb skala upp - du kan, i konstant tid, skala upp dina beräkningsresurser för att rymma tunga arbetsbelastningar om och när det behövs, och sedan skala beräkningsresurserna tillbaka när det inte behövs.

Tjänstnivån Hyperskala tar bort många av de praktiska gränser som traditionellt setts i molndatabaser. Om de flesta andra databaser begränsas av de resurser som är tillgängliga i en enda nod, har databaser på tjänstnivån Hyperskala inga sådana gränser. Med sin flexibla lagringsarkitektur växer lagringen efter behov. Faktum är att hyperskala databaser inte skapas med en definierad maxstorlek. En hyperskala databas växer efter behov - och du debiteras endast för den kapacitet du använder. För läsintensiva arbetsbelastningar ger tjänstnivån hyperskala snabb utskalning genom att etablera ytterligare läsrepliker efter behov för avlastning av läsarbetsbelastningar.

Dessutom är den tid som krävs för att skapa säkerhetskopiering av databaser eller för att skala upp eller ned inte längre knuten till datavolymen i databasen. Hyperskala databaser kan säkerhetskopieras nästan omedelbart. Du kan också skala en databas i tiotals terabyte upp eller ned på några minuter. Den här funktionen befriar dig från problem med att bli inrutad av dina första konfigurationsalternativ.

Mer information om beräkningsstorlekarna för tjänstnivån Hyperskala finns i [Egenskaper för tjänstnivå](sql-database-service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Vem bör överväga tjänstnivån Hyperskala

Tjänstnivån Hyperskala är avsedd för de flesta affärsarbetsbelastningar eftersom den ger stor flexibilitet och hög prestanda med oberoende skalbara beräknings- och lagringsresurser. Med möjligheten att automatiskt skala lagring upp till 100 TB är det ett utmärkt val för kunder som:

- Ha stora databaser lokalt och vill modernisera sina program genom att flytta till molnet
- Finns redan i molnet och begränsas av de maximala begränsningarna för databasstorlek för andra tjänstnivåer (1–4 TB)
- Har mindre databaser, men kräver snabb vertikal och horisontell beräkningsskalning, hög prestanda, omedelbar säkerhetskopiering och snabb databasåterställning.

Tjänstnivån Hyperskala stöder ett brett spektrum av SQL Server-arbetsbelastningar, från ren OLTP till ren analys, men den är främst optimerad för OLTP- och hybridtransaktions- och analysbearbetningsarbetsbelastningar (HTAP).

> [!IMPORTANT]
> Elastiska pooler stöder inte servicenivån hyperskala.

## <a name="hyperscale-pricing-model"></a>Prismodell för hyperskala

Hyperskala tjänstnivå är endast tillgänglig i [vCore-modellen](sql-database-service-tiers-vcore.md). Prismodellen skiljer sig något från servicenivåerna för allmänt syfte eller Affärskritiskt för att anpassa sig till den nya arkitekturen:

- **Beräkna:**

  Hyperskala beräkningsenhetspriset är per replik. [Azure Hybrid-förmånspriset](https://azure.microsoft.com/pricing/hybrid-benefit/) tillämpas på lässkalerepliker automatiskt. Vi skapar en primär replik och en skrivskyddad replik per hyperskala databas som standard.  Användare kan justera det totala antalet repliker inklusive den primära från 1-5.

- **Lagring:**

  Du behöver inte ange den maximala datastorleken när du konfigurerar en hyperskaladatabas. På hyperskalenivån debiteras du för lagring för databasen baserat på faktisk allokering. Lagring allokeras automatiskt mellan 40 GB och 100 TB, i steg om 10 GB 10 GB. Flera datafiler kan växa samtidigt om det behövs. En hyperskala databas skapas med en startstorlek på 10 GB och den börjar växa med 10 GB var 10:e minut, tills den når storleken 40 GB.

Mer information om hyperskala priser finns i [Azure SQL Database Pricing](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arkitektur för distribuerade funktioner

Till skillnad från traditionella databasmotorer som har centraliserat alla datahanteringsfunktioner på en plats/process (även så kallade distribuerade databaser i produktion idag har flera kopior av en monolitisk datamotor), separerar en hyperskala databas frågebehandlingsmotorn, där semantiken hos olika datamotorer avviker från de komponenter som ger långsiktig lagring och hållbarhet för data. På så sätt kan lagringskapaciteten skalas smidigt ut så långt det behövs (det ursprungliga målet är 100 TB). Skrivskyddade repliker delar samma lagringskomponenter så ingen datakopia krävs för att skapa en ny läsbar replik. 

Följande diagram illustrerar de olika typerna av noder i en hyperskaladatabas:

![Arkitektur](./media/sql-database-hyperscale/hyperscale-architecture.png)

En hyperskaladatabas innehåller följande olika typer av komponenter:

### <a name="compute"></a>Compute

Beräkningsnoden är där relationsmotorn finns, så alla språkelement, frågebearbetning och så vidare inträffar. Alla användarinteraktioner med en hyperskaladatabas sker via dessa beräkningsnoder. Beräkningsnoder har SSD-baserade cacheminnen (märkta RBPEX - Fjädrande buffertpooltillägg i föregående diagram) för att minimera antalet nätverksrundningsturer som krävs för att hämta en sida med data. Det finns en primär beräkningsnod där alla läs-skriv-arbetsbelastningar och transaktioner bearbetas. Det finns en eller flera sekundära beräkningsnoder som fungerar som hot standby-noder för redundansändamål, samt fungerar som skrivskyddade beräkningsnoder för avlastning av läsarbetsbelastningar (om den här funktionen önskas).

### <a name="page-server"></a>Sidserver

Sidservrar är system som representerar en utskalad lagringsmotor.  Varje sidserver ansvarar för en delmängd av sidorna i databasen.  Nominellt styr varje sidserver mellan 128 GB och 1 TB data. Inga data delas på mer än en sidserver (utanför repliker som sparas för redundans och tillgänglighet). Jobbet för en sidserver är att visa databassidor ut till beräkningsnoderna på begäran och att hålla sidorna uppdaterade när transaktioner uppdaterar data. Sidservrar hålls uppdaterade genom att spela loggposter från loggtjänsten. Sidservrar underhåller också SSD-baserade cacheminnen för att förbättra prestanda. Långsiktig lagring av datasidor sparas i Azure Storage för ytterligare tillförlitlighet.

### <a name="log-service"></a>Loggtjänst

Loggtjänsten accepterar loggposter från den primära beräkningsrepliken, beständiga dem i en varaktig cache och vidarebefordrar loggposterna till resten av beräkningsrepliker (så att de kan uppdatera sina cacheminnen) samt relevanta sidserver(er), så att data kan uppdateras där. På så sätt sprids alla dataändringar från den primära beräkningsrepliken via loggtjänsten till alla sekundära beräkningsrepliker och sidservrar. Slutligen trycks loggposterna ut till långsiktig lagring i Azure Storage, som är en praktiskt taget oändlig lagringsdatabas. Den här mekanismen tar bort behovet av frekvent logg trunkering. Loggtjänsten har också lokal cache för att påskynda åtkomsten till loggposter.

### <a name="azure-storage"></a>Azure Storage

Azure Storage innehåller alla datafiler i en databas. Sidservrar håller datafiler i Azure Storage uppdaterade. Den här lagringen används för säkerhetskopieringsändamål, samt för replikering mellan Azure-regioner. Säkerhetskopior implementeras med hjälp av ögonblicksbilder av datafiler. Återställningsåtgärder med ögonblicksbilder är snabba oavsett datastorlek. Data kan återställas till vilken tidpunkt som helst inom databasens lagringsperiod för säkerhetskopiering.

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Säkerhetskopior är fil-snapshot baserade och därmed de är nästan ögonblickliga. Lagrings- och beräkningsseparation gör det möjligt att trycka ned säkerhetskopierings-/återställningsåtgärden till lagringslagret för att minska bearbetningsbördan på den primära beräkningsrepliken. Därför påverkar säkerhetskopiering av databaser inte prestanda för den primära beräkningsnoden. På samma sätt görs återställningar genom att återgå till ögonblicksbilder av filer och som sådan inte är en storlek på dataåtgärd. Återställning är en konstant tidsåtgärd och till och med databaser med flera terabyte kan återställas på några minuter i stället för timmar eller dagar. Skapandet av nya databaser genom att återställa en befintlig säkerhetskopia utnyttjar också den här funktionen: att skapa databaskopior för utvecklings- eller testningsändamål, även av databaser av terabytestorlek, kan inte gå att göra på några minuter.

## <a name="scale-and-performance-advantages"></a>Fördelar med skalning och prestanda

Med möjligheten att snabbt snurra upp/ner ytterligare skrivskyddade beräkningsnoder tillåter hyperskalearkitekturen betydande lässkalafunktioner och kan också frigöra den primära beräkningsnoden för att betjäna fler skrivbegäranden. Dessutom kan beräkningsnoderna skalas upp/ned snabbt på grund av arkitekturen med delad lagring i hyperskalaarkitekturen.

## <a name="create-a-hyperscale-database"></a>Skapa en hyperskaladatabas

En hyperskala databas kan skapas med hjälp av [Azure-portalen](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Hyperskala databaser är endast tillgängliga med hjälp av den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

Följande T-SQL-kommando skapar en hyperskaladatabas. Du måste ange både utgåva `CREATE DATABASE` och servicemål i utdraget. Se [resursgränserna](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) för en lista över giltiga servicemål.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Detta kommer att skapa en hyperskala databas på Gen5 hårdvara med 4 kärnor.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrera en befintlig Azure SQL-databas till tjänstnivån Hyperskala

Du kan flytta dina befintliga Azure SQL-databaser till Hyperskala med [Azure-portalen,](https://portal.azure.com) [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Just nu är detta en enkelriktad migration. Du kan inte flytta databaser från hyperskala till en annan tjänstnivå, förutom genom att exportera och importera data. För proofs of concept (POCs) rekommenderar vi att du gör en kopia av dina produktionsdatabaser och migrerar kopian till Hyperskala. Att migrera en befintlig Azure SQL-databas till hyperskalenivån är en storlek på dataåtgärd.

Följande T-SQL-kommando flyttar en databas till tjänstnivån Hyperskala. Du måste ange både utgåva `ALTER DATABASE` och servicemål i utdraget.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Ansluta till en lässkalareplik av en hyperskaladatabas

I Hyperskala databaser `ApplicationIntent` avgör argumentet i anslutningssträngen som tillhandahålls av klienten om anslutningen dirigeras till skrivrepliken eller till en skrivskyddad sekundär replik. Om `ApplicationIntent` uppsättningen `READONLY` till och databasen inte har en sekundär replik dirigeras anslutningen till `ReadWrite` den primära repliken och fungerar som standard.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Hyperskala sekundära repliker är alla identiska, med samma servicenivåmål som den primära repliken. Om det finns mer än en sekundär replik distribueras arbetsbelastningen över alla tillgängliga sekundärfiler. Varje sekundär replik uppdateras oberoende av varandra, vilket innebär att olika repliker kan ha olika datafördröjning i förhållande till den primära repliken.

## <a name="database-high-availability-in-hyperscale"></a>Databas hög tillgänglighet i hyperskala

Precis som på alla andra tjänstnivåer garanterar Hyperskala datahållbarhet för genomförda transaktioner oavsett beräkningsrepliktillgänglighet. Omfattningen av driftstopp på grund av att den primära repliken blir otillgänglig beror på vilken typ av redundans (planerad kontra oplanerad) och på förekomsten av minst en sekundär replik. I en planerad redundans (d.v.s. en underhållshändelse) skapar systemet antingen den nya primära repliken innan en redundans initieras eller en befintlig sekundär replik som redundansmål. I en oplanerad redundans (d.v.s. ett maskinvarufel på den primära repliken) använder systemet en sekundär replik som ett redundansmål om det finns eller skapar en ny primär replik från poolen med tillgänglig beräkningskapacitet. I det senare fallet är stilleståndstiden längre på grund av extra steg som krävs för att skapa den nya primära repliken.

Mer information om SLA för hyperskala finns i [SLA för Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Haveriberedskap för hyperskala databaser

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Återställa en hyperskaladatabas till en annan geografi
Om du behöver återställa en Azure SQL Database Hyperscale DB till en annan region än den som den för närvarande finns i, som en del av en haveriberedskapsåtgärd eller åtgärd, flyttning eller någon annan orsak, är den primära metoden att göra en geo-återställning av databasen.  Detta innebär exakt samma steg som vad du skulle använda för att återställa andra AZURE SQL DB till en annan region:
1. Skapa en SQL Database-server i målområdet om du inte redan har en lämplig server där.  Den här servern ska ägas av samma prenumeration som den ursprungliga (källservern).
2. Följ instruktionerna i [geo-återställningsavsnittet](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) på sidan om hur du återställer Azure SQL-databaser från automatiska säkerhetskopieringar.

> [!NOTE]
> Eftersom källan och målet finns i separata regioner kan databasen inte dela ögonblicksbildlagring med källdatabasen som i icke-geo-återställningar, som slutförs extremt snabbt. När det gäller en geoåterställning av en hyperskaladatabas är det en datastorlek, även om målet är i det parade området för den georepnaderade lagringen.  Det innebär att det tar tid att återställa en geo-återställning till storleken på den databas som återställs.  Om målet finns i den parade regionen kommer kopian att finnas inom en region, vilket kommer att vara betydligt snabbare än en korsregionkopia, men det kommer fortfarande att vara en storleksåtgärd.

## <a name="available-regions"></a><a name=regions></a>Tillgängliga regioner

Hyperskalningsnivån för Azure SQL Database är för närvarande tillgänglig i följande regioner:

- Australien, östra
- Australien, sydöstra
- Brasilien, södra
- Kanada, centrala
- USA, centrala
- Kina Öst 2
- Kina Norra 2
- Asien, östra
- USA, östra
- Östra Usa 2
- Frankrike, centrala
- Japan, östra
- Japan, västra
- Sydkorea, centrala
- Sydkorea, södra
- USA, norra centrala
- Europa, norra
- Sydafrika North
- USA, södra centrala
- Sydostasien
- Storbritannien, södra
- Storbritannien, västra
- Europa, västra
- USA, västra
- USA, västra 2

Om du vill skapa hyperskala databas i en region som inte är listad som stöds, kan du skicka en begäran om introduktion via Azure-portalen. Instruktioner finns i [Begär kvotökningar för Azure SQL Database](quota-increase-request.md) för instruktioner. När du skickar in din begäran använder du följande riktlinjer:

- Använd kvottypen [Annan kvotbegäran för](quota-increase-request.md#other) kvot för kvot.
- Lägg till beräknings-SKU/totalkärnor inklusive läsbara repliker i textinformationen.
- Ange också den uppskattade TB.

## <a name="known-limitations"></a>Kända begränsningar

Dessa är de aktuella begränsningarna för tjänstnivån Hyperskala från och med GA.  Vi arbetar aktivt med att ta bort så många av dessa begränsningar som möjligt.

| Problem | Beskrivning |
| :---- | :--------- |
| Fönstret Hantera säkerhetskopior för en logisk server visar inte att hyperskalningsdatabaser filtreras från SQL-servern  | Hyperskala har en separat metod för att hantera säkerhetskopior, och som sådan den långsiktiga kvarhållning och punkt i tid backup Lagringsinställningar inte gäller / är ogiltiga. Hyperskaladatabaser visas därför inte i fönstret Hantera säkerhetskopiering. |
| Återställning från tidpunkt | Du kan återställa en hyperskaladatabas till en databas som inte är hyperskallig inom lagringsperioden som inte är hyperskala. Du kan inte återställa en databas som inte är hyperskala till en hyperskaladatabas.|
| Om en databas har en eller flera datafiler som är större än 1 TB misslyckas migreringen | I vissa fall kan det vara möjligt att undvika problemet genom att krympa de stora filerna till mindre än 1 TB. Om du migrerar en databas som används under migreringsprocessen kontrollerar du att ingen fil blir större än 1 TB. Använd följande fråga för att bestämma storleken på databasfiler. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Managed Instance | Azure SQL Database Managed Instance stöds för närvarande inte med hyperskaladatabaser. |
| Elastiska pooler |  Elastiska pooler stöds för närvarande inte med SQL Database Hyperscale.|
| Migrering till hyperskala är för närvarande en enkelriktad åtgärd | När en databas har migrerats till Hyperskala kan den inte migreras direkt till en tjänstnivå som inte är hyperskallig. För närvarande är det enda sättet att migrera en databas från Hyperskala till icke-hyperskala att exportera/importera med hjälp av en BACPAC-fil eller andra dataförflyttningstekniker (Masskopiering, Azure Data Factory, Azure Databricks, SSIS, etc.)|
| Migrering av databaser med beständiga minnesobjekt | Hyperskala stöder endast icke-beständiga in-memory-objekt (tabelltyper, inbyggda SPs och funktioner).  Beständiga minnestabeller och andra objekt måste tas bort och återskapas som icke-minne-objekt innan en databas migreras till tjänstnivån Hyperskala.|
| Spårning av ändringar | Ändringsspårning är för närvarande i offentlig förhandsversion och kan aktiveras på nya eller befintliga hyperskaladatabaser. |
| Geo Replikering  | Du kan ännu inte konfigurera geo-replikering för Azure SQL Database Hyperscale. |
| Databaskopia | Du kan ännu inte använda Databaskopia för att skapa en ny databas i Azure SQL Hyperscale. |
| TDE/AKV-integrering | Transparent databaskryptering med Azure Key Vault (kallas vanligen Bring-Your-Own-Key eller BYOK) stöds ännu inte för Azure SQL Database Hyperscale, men TDE med tjänsthanterade nycklar stöds fullt ut. |
|Intelligenta databasfunktioner | Med undantag för alternativet "Tvångsplan" stöds ännu inte alla andra alternativ för automatisk justering på Hyperskala: alternativ kan tyckas vara aktiverade, men det kommer inte att finnas några rekommendationer eller åtgärder som görs. |
|Information om frågeprestanda | Frågeprestandastatistik stöds för närvarande inte för hyperskaledatabaser. |
| Krympa databas | DBCC SHRINKDATABASE eller DBCC SHRINKFILE stöds för närvarande inte för hyperskaladatabaser. |
| Kontroll av databasens integritet | DBCC CHECKDB stöds för närvarande inte för hyperskaladatabaser. Mer information om hantering av dataintegritet i Azure SQL Database finns [i Dataintegritetsintegritet](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) i Azure SQL Database. |

## <a name="next-steps"></a>Nästa steg

- Vanliga frågor och svar om hyperskala finns [i Vanliga frågor och svar om Hyperskala](sql-database-service-tier-hyperscale-faq.md).
- Information om tjänstnivåer finns i [Tjänstnivåer](sql-database-service-tiers.md)
- Se [Översikt över resursgränser på en logisk server](sql-database-resource-limits-logical-server.md) för information om begränsningar på server- och prenumerationsnivå.
- Information om inköp av modellgränser för en enskild databas finns i [Azure SQL Database vCore-baserade inköpsmodellgränser för en enskild databas](sql-database-vcore-resource-limits-single-databases.md).
- En lista över funktioner och jämförelser finns i [vanliga SQL-funktioner](sql-database-features.md).
