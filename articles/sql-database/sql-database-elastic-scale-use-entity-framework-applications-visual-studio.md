---
title: Klientbibliotek för elastisk databas med Entity Framework | Microsoft Docs
description: Använd klientbibliotek för elastisk databas och Entity Framework för att koda databaser
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: fba872b01d4ddf0bb4e6aa8d0217042617688b8a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Klientbibliotek för elastisk databas med Entity Framework
Det här dokumentet beskrivs ändringarna i ett Entity Framework-program som behövs för att integrera med den [elastisk Databasverktyg](sql-database-elastic-scale-introduction.md). Fokus ligger på fastställdes [Fragmentera kartan management](sql-database-elastic-scale-shard-map-management.md) och [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md) med Entity Framework **Code First** metod. Den [Code först - ny databas](http://msdn.microsoft.com/data/jj193542.aspx) självstudier för EF fungerar som exemplet körs i hela dokumentet. Exempelkoden åtföljer det här dokumentet är en del av verktygen för elastisk databas uppsättning exempel i Visual Studio-kodexempel.

## <a name="downloading-and-running-the-sample-code"></a>Hämta och kör exempelkoden
Hämta koden för den här artikeln:

* Visual Studio 2012 eller senare krävs. 
* Hämta den [elastisk DB-verktyg för Azure SQL - Entity Framework-integrering exempel](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) på MSDN. Packa upp exemplet på en plats som du väljer.
* Starta Visual Studio. 
* Välj Arkiv -> Öppna projekt/lösning i Visual Studio. 
* I den **Open Project** dialogrutan navigerar du till exempel som du hämtade och välj **EntityFrameworkCodeFirst.sln** att öppna exemplet. 

Om du vill köra exemplet måste du skapa tre tomma databaser i Azure SQL Database:

* Fragmentera kartan Manager-databasen
* Fragmentera 1-databasen
* Fragmentera 2-databas

När du har skapat dessa databaser, Fyll i platshållare i **Program.cs** med din Azure SQL DB-servernamnet, databasnamn och autentiseringsuppgifter när du ansluter till databaserna. Skapa lösningen i Visual Studio. Visual Studio hämtar nödvändiga NuGet-paketen för klientbibliotek för elastisk databas Entity Framework och tillfälligt fel hantering som en del av skapar. Se till att återställa NuGet-paket har aktiverats för din lösning. Du kan aktivera den här inställningen genom att högerklicka på lösningsfilen i Visual Studio Solution Explorer. 

## <a name="entity-framework-workflows"></a>Entity Framework-arbetsflöden
Entity Framework utvecklare förlitar sig på något av följande fyra arbetsflöden för att skapa program och säkerställa persistence för programobjekt: 

* **Code första (ny databas)**: det EF utvecklare skapar modellen i programkoden och sedan EF genererar databasen från den. 
* **Code första (befintlig databas)**: utvecklaren kan EF generera programkoden för modellen från en befintlig databas.
* **Modellen första**: utvecklare skapar modellen i EF designer och EF skapar sedan databasen från modellen.
* **Databasen första**: utvecklaren använder EF tooling för att härleda modellen från en befintlig databas. 

Dessa metoder är beroende av DbContext-klassen för att hantera transparent databasanslutningar och databasschemat för ett program. Olika konstruktorer på basklassen DbContext Tillåt för olika nivåer av kontroll över anslutningen skapas, startprogram för databasen och skapa schemat. Utmaningar uppkommer främst att anslutningen databashantering tillhandahålls av EF korsar med anslutning hanteringsfunktioner i data-beroende routning gränssnitt som anges av klientbiblioteket för elastisk databas. 

## <a name="elastic-database-tools-assumptions"></a>Förutsättningar för verktyg för elastisk databas
Termdefinitioner finns [elastisk databas verktyg ordlista](sql-database-elastic-scale-glossary.md).

Med klientbibliotek för elastisk databas, kan du definiera partitioner för dina programdata som kallas shardlets. Shardlets identifieras med en nyckel för horisontell partitionering och mappas till specifika databaser. Ett program kan ha valfritt antal databaser efter behov och distribuera shardlets för att ge tillräckligt med kapacitet eller prestanda angivna aktuella affärsbehov. Mappningen för horisontell partitionering nyckelvärden till databaserna lagras av en Fragmentera karta som tillhandahålls av elastisk databas-klientens API: er. Den här funktionen kallas **Fragmentera kartan Management**, eller SMM för kort. Fragmentera kartan fungerar också som Service broker för databasanslutningar för begäranden som innehåller en nyckel för horisontell partitionering. Den här funktionen kallas **data beroende routning**. 

Fragmentera kartan manager skyddar användare från inkonsekvent vyer i shardlet data som kan uppstå när samtidiga shardlet hanteringsåtgärder (till exempel flytta data från en Fragmentera till en annan) sker. Om du vill göra det, av Fragmentera mappningar klienten biblioteket broker databasanslutningar för ett program. Detta gör att funktionen Fragmentera kartan att avsluta en databasanslutning automatiskt när Fragmentera hanteringsåtgärder kan påverka shardlet som anslutningen har skapats för. Den här metoden behöver integrera med några av EFS funktioner, till exempel skapa nya anslutningar från en befintlig för att kontrollera om databasen finns. I allmänhet har våra observationer som standard DbContext konstruktorer bara fungerar på ett tillförlitligt sätt för stängd databasanslutningar som kan klonas på ett säkert sätt för EF fungerar. Principen designen för elastisk databas är i stället att endast broker öppna anslutningar. En tror att stänga en anslutning som asynkrona av klientbiblioteket innan överlämnande till EF DbContext kan lösa problemet. Genom att stänga anslutningen och förlita dig på EF att öppna den igen, kan en dock foregoes validering och konsekvens kontroller som utförs av biblioteket. Funktionen migreringar i EF, använder dock dessa anslutningar för att hantera underliggande databasschemat på ett sätt som är transparent för programmet. Helst bör du behålla och kombinera alla dessa funktioner från både klientbibliotek för elastisk databas och EF i samma program. I följande avsnitt beskrivs de här egenskaperna och kraven i detalj. 

## <a name="requirements"></a>Krav
När du arbetar med både klientbibliotek för elastisk databas och Entity Framework-API: er som du vill behålla följande egenskaper: 

* **Skalbar**: Lägg till eller ta bort databaser från datanivå av delat programmet efter behov för kapacitetskraven för programmet. Det innebär att kontroll över skapa och ta bort databaser och hanterar databaser och mappningar av shardlets med elastisk databas Fragmentera kartan manager API: er. 
* **Konsekvenskontroll**: programmet använder horisontell partitionering och använder funktioner för routning för data-beroende av klientbiblioteket. För att undvika skador eller fel frågeresultat asynkrona anslutningar via hanteraren Fragmentera kartan. Detta bevarar validering och konsekvens.
* **Code första**: att behålla att underlätta för EFS kod första paradigmet. I den första koden mappas klasser i programmet transparent till de underliggande databasstrukturer. Programkoden samverkar med DbSets som maskerar de flesta aspekter som ingår i den underliggande databasen bearbetningen.
* **Schemat**: Entity Framework hanterar inledande databasen schemat skapas och efterföljande schemat utvecklingen via migreringar. Behålla dessa funktioner, är anpassa din app enkelt som utvecklas av data. 

Följande riktlinjer anger hur uppfyller dessa krav för Code First program med elastiska Databasverktyg. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Data dependent routning med EF DbContext
Databasanslutningar med Entity Framework vanligtvis hanteras via underklasser för **DbContext**. Skapa dessa underklasser som härleds från **DbContext**. Är där du definierar din **DbSets** som implementerar databasen har säkerhetskopierats samlingar av CLR-objekt för tillämpningsprogrammet. Du kan identifiera flera användbara egenskaper som inte nödvändigtvis har för andra EF code första Programscenarier i samband med data beroende routning: 

* Databasen har redan finns och har registrerats i elastisk databas Fragmentera kartan. 
* Schemat för programmet har redan distribuerats till databasen (beskrivs nedan). 
* Data dependent anslutningar till databasen asynkrona Fragmentera Map. 

Integrera **DbContexts** med data beroende routning för skalbar:

1. Skapa fysiska databasanslutningar via elastisk databas klienten gränssnitt av hanteraren Fragmentera karta 
2. Omsluta anslutningen med den **DbContext** underklass
3. Skicka anslutningen till den **DbContext** basera klasser för att se till att all bearbetning på EF sida samt händer. 

I följande kodexempel visas den här metoden. (Den här koden är också i det medföljande Visual Studio-projektet)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data-dependent routing. This call opens a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call fails for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Huvudpunkter
* En ny konstruktor ersätter standardkonstruktor i DbContext-underklassen 
* Konstruktorn new tar argument som krävs för data-beroende Routning via klientbibliotek för elastisk databas:
  
  * Fragmentera kartan för att komma åt routning gränssnitt för data-beroende
  * nyckeln delning för att identifiera shardlet
  * en anslutningssträng med autentiseringsuppgifterna för data-beroende routning anslutningen till Fragmentera. 
* Anropet till konstruktorn basklassen tar en detour i en statisk metod som utför alla steg som krävs för data-beroende routning. 
  
  * Den använder OpenConnectionForKey anropet av gränssnitten för elastisk databas-klienten på kartan Fragmentera för att upprätta en öppen anslutning.
  * Fragmentera kartan skapas en öppen anslutning till Fragmentera som innehåller shardlet för den angivna horisontell partitionering nyckeln.
  * Öppna anslutningen skickas tillbaka till konstruktorn basklass för DbContext att indikera att anslutningen kommer att användas av EF i stället för att låta EF automatiskt skapa en ny anslutning. Det här sättet anslutningen har taggats av klienten API för elastisk databas så att den kan garantera konsekvens under Fragmentera kartan hanteringsåtgärder.

Använd konstruktorn new för DbContext-underklass i stället för Standardkonstruktorn i koden. Här är ett exempel: 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

Konstruktorn new öppnar anslutningen till Fragmentera som lagrar data för shardlet som identifieras av värdet för **tenantid1**. Koden i den **med** block förblir oförändrat för att få åtkomst till den **DbSet** för bloggar med EF på Fragmentera för **tenantid1**. Detta ändrar semantik för koden i med blockera så att alla databasåtgärder har nu begränsats till en Fragmentera där **tenantid1** sparas. Till exempel en LINQ-fråga över bloggarna **DbSet** bara returnera bloggar som lagras på den aktuella Fragmentera, men inte de som finns på andra delar.  

#### <a name="transient-faults-handling"></a>Tillfälliga problem hantering
Microsoft Patterns & Practices teamet publicerade den [på tillfälliga fel hanterar programmet Block](https://msdn.microsoft.com/library/dn440719.aspx). Biblioteket används med elastisk skalbarhet klientbiblioteket i kombination med EF. Se dock till att alla tillfälligt undantag returnerar till en plats där du kan se till att den nya konstruktorn används efter ett tillfälligt fel så att alla nya anslutningsförsök görs med hjälp av du tweaked konstruktorer. Annars en anslutning till rätt Fragmentera är inte säkert och det finns inga garantier som anslutningen bibehålls när det sker ändringar till Fragmentera karta. 

Följande kodexempel illustrerar hur du kan använda en SQL-återförsöksprincip runt den nya **DbContext** underklasskonstruktorer: 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** i koden ovan har definierats som en **SqlDatabaseTransientErrorDetectionStrategy** med ett återförsöksvärde på 10 och 5 sekunder väntetid mellan återförsök. Den här metoden liknar vägledning för EF och användarinitierad transaktioner (se [begränsningar med du försöker köra strategier (och senare EF6)](http://msdn.microsoft.com/data/dn307226). Båda situationer kräver att programmet styr omfånget som tillfälligt undantag returnerar: antingen öppna transaktionen eller återskapa kontext från rätt konstruktor (som visas) som använder klientbibliotek för elastisk databas.

Behovet av att styra där tillfälligt undantag ta oss tillbaka i omfånget utesluter också användning av inbyggt **SqlAzureExecutionStrategy** som medföljer EF. **SqlAzureExecutionStrategy** skulle öppna en anslutning utan att använda **OpenConnectionForKey** och därför kringgå verifieringen som utförs som en del av den **OpenConnectionForKey**anropa. Kodexemplet används i stället inbyggt **DefaultExecutionStrategy** som medföljer också EF. Inte **SqlAzureExecutionStrategy**, den fungerar korrekt i kombination med återförsöksprincip tillfälliga fel hanterar. Körningsprincipen har angetts i den **ElasticScaleDbConfiguration** klass. Observera att vi beslutat att använda **DefaultSqlExecutionStrategy** eftersom föreslås med **SqlAzureExecutionStrategy** om tillfälligt undantag inträffar - som skulle leda till felaktig beteende enligt beskrivningen. Mer information om annan retry principer och EF finns [Connection Resiliency i EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Konstruktorn omskrivningar
Koden exemplen ovan visar de standard konstruktorn skriver krävs för ditt program för att kunna använda data beroende routning med Entity Framework. I följande tabell är en generalisering den här metoden för andra konstruktorer. 

| Aktuella konstruktor | Omskrivet konstruktor för data | Grundläggande konstruktor | Anteckningar |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection bool) |Anslutningen måste vara en funktion av Fragmentera kartan och nyckeln data beroende routning. Du behöver för att hoppa över automatisk anslutning skapande av EF och i stället använda Fragmentera kartan för att broker anslutningen. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection bool) |Anslutningen är en funktion av Fragmentera kartan och nyckeln data beroende routning. Fasta databasrollen namn eller anslutningssträngen fungerar inte som de hoppa över verifieringen av Fragmentera kartan. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection DbCompiledModel, bool) |Anslutningen skapas med mallen för den angivna Fragmentera karta och horisontell partitionering nyckeln. Kompilerade modellen överförs till grundläggande c'tor. |
| MyContext (DbConnection bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext (DbConnection bool) |Anslutningen måste härledas från Fragmentera kartan och nyckeln. Det går inte att ange indata (såvida inte dessa indata har redan använder Fragmentera kartan och nyckeln). Booleskt skickas vidare. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection DbCompiledModel, bool) |Anslutningen måste härledas från Fragmentera kartan och nyckeln. Det går inte att ange indata (såvida inte dessa indata använde Fragmentera kartan och nyckel). Kompilerade modellen skickas vidare. |
| MyContext (ObjectContext bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext bool) |Konstruktorn new måste se till att alla anslutningen i ObjectContext skickas som indata dirigeras till en anslutning som hanteras av elastisk skalbarhet. En detaljerad beskrivning av ObjectContexts är utanför omfattningen för det här dokumentet. |
| MyContext (DbConnection DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection DbCompiledModel, bool;) |Anslutningen måste härledas från Fragmentera kartan och nyckeln. Anslutningen kan inte anges som en inmatning (såvida inte dessa indata har redan använder Fragmentera kartan och nyckeln). Modellen och booleskt värde skickas till konstruktorn basklass. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Fragmentera schemat distribution via EF migrering
Schemahantering av automatiska är i syfte att underlätta tillhandahålls av Entity Framework. I samband med program med elastiska Databasverktyg som du vill behålla den här funktionen för att automatiskt etablera schemat till nyligen skapade shards när databaserna läggs till delat programmet. Det primära användningsfallet är att öka kapaciteten på datanivå för delat program som använder EF. Förlita dig på EFS funktioner för schemahantering av minskar databasen administration arbete med ett delat program som bygger på EF. 

Schemat distribution via EF migreringar fungerar bäst med **inte öppnats anslutningar**. Detta skiljer sig från det scenario för data-beroende routning som förlitar sig på öppnade anslutningen från klienten API för elastisk databas. En annan skillnaden är konsekvent kravet: när önskvärt att garantera konsekvens för alla anslutningar för data-beroende att skydda mot samtidiga Fragmentera kartan manipulering, det är inte ett problem med inledande schemat distribution till en ny databas som har ännu inte har registrerats i kartan Fragmentera och ännu inte tilldelats håller shardlets. Du kan därför förlita dig på vanlig databasanslutningar för det här scenariot, till skillnad från data beroende routning.  

Detta leder till en metod där schemat distribution via EF migreringar är direkt kopplade till registreringen av den nya databasen som en Fragmentera i programavbildningen Fragmentera. Detta är beroende av följande krav: 

* Databasen har redan skapats. 
* Databasen är tom, det innehåller inget användarschema och inga användardata.
* Databasen kan inte ännu nås via elastisk databas-klientens API: er för data-beroende routning. 

Med dessa krav är på plats, kan du skapa en vanlig icke öppnade **SqlConnection** till startar EF migrering för distribution av schema. Följande kodexempel illustrerar den här metoden. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 


Det här exemplet visar metoden **RegisterNewShard** som registrerar Fragmentera i kartan Fragmentera distribuerar schemat via EF migreringar och lagrar en mappning av en nyckel för horisontell partitionering till Fragmentera. Det är beroende av en konstruktor med den **DbContext** underklass (**ElasticScaleContext** i exemplet) som tar en SQL-anslutningssträng som indata. Koden för den här konstruktorn är enkel, vilket visas i följande exempel: 

        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // You want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 

En kan ha använt version i konstruktorn som ärvts från basklassen. Men koden måste se till att standard initieraren för EF används vid anslutning. Därför detour kort i den statiska metoden innan anropet till basklassen konstruktorn med anslutningssträngen. Observera att registreringen av shards ska köras i en annan programdomän eller processen för att se till att inställningarna för EF initieraren inte står i konflikt. 

## <a name="limitations"></a>Begränsningar
De metoder som beskrivs i det här dokumentet medför några begränsningar: 

* EF-program som använder **LocalDb** måste först att migrera till en vanlig SQL Server-databas innan du använder klientbibliotek för elastisk databas. Skala ut ett program via delning med elastisk skalbarhet är inte möjligt med **LocalDb**. Observera att utveckling kan fortfarande använda **LocalDb**. 
* Ändringar i programmet som implicerar schemaändringar för databasen måste du gå igenom EF migreringar på alla delar. Exempelkod för det här dokumentet visar inte hur du gör detta. Överväg att använda Update-databasen med en parameter för ConnectionString och iterera över alla delar; eller extrahera T-SQL-skript för väntande migreringen använder Update-databas med - skriptet alternativet och tillämpa T-SQL-skript på din shards.  
* Få en begäran, förutsätts att alla dess databasbearbetning ingår i en enda Fragmentera som identifieras av horisontell partitionering-nyckel som tillhandahålls av begäran. Men har detta antagande inte alltid värdet true. Till exempel när det går inte att göra en nyckel för horisontell partitionering tillgänglig. För att lösa det klientbiblioteket tillhandahåller den **MultiShardQuery** klassen som implementerar en anslutning abstraktion för frågor över flera delar. Lär dig att använda den **MultiShardQuery** i kombination med EF är utanför omfattningen för det här dokumentet

## <a name="conclusion"></a>Sammanfattning
Via de steg som beskrivs i det här dokumentet EF program kan använda det klientbibliotek för elastisk databas kapaciteten för data-beroende routning av eftersom konstruktorerna i den **DbContext** underklasser som används i EF-programmet. Detta begränsar de ändringar som krävs för att de platserna där **DbContext** klasser finns redan. Dessutom kan EF program fortsätta att dra nytta av schemat för automatisk distribution genom att kombinera de steg som anropar nödvändiga EF-migrering med registrering av nya delar och avbildningar i kartan Fragmentera. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
