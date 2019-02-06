---
title: Med elastic database-klientbibliotek med Entity Framework | Microsoft Docs
description: Använd Elastic Database-klientbiblioteket och Entity Framework för att koda databaser
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/04/2019
ms.openlocfilehash: 54890aef8dabfa019a5181c155b6668b1c07cf2c
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755942"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Elastic Database-klientbibliotek med Entity Framework

Det här dokumentet visar de ändringar i ett Entity Framework-program som behövs för att integrera med den [elastiska Databasverktyg](sql-database-elastic-scale-introduction.md). Fokus ligger på att skriva [fragmentkarthantering](sql-database-elastic-scale-shard-map-management.md) och [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md) med Entity Framework **Code First** metod. Den [Code först – ny databas](https://msdn.microsoft.com/data/jj193542.aspx) självstudien för EF fungerar som exemplet som körs i hela dokumentet. Exempelkoden som åtföljer det här dokumentet är en del av verktygen för elastiska databaser uppsättning exempel i Visual Studio-kodexempel.

## <a name="downloading-and-running-the-sample-code"></a>Ladda ned och kör exempelkoden

Så här hämtar du koden för den här artikeln:

* Visual Studio 2012 eller senare krävs. 
* Ladda ned den [elastiska DB-verktyg för Azure SQL - integrering av Entity Framework exempel](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) från MSDN. Packa upp exemplet på en plats som du väljer.
* Starta Visual Studio. 
* I Visual Studio väljer du Arkiv -> Öppna projekt/lösning. 
* I den **öppna projekt** dialogrutan, navigera till exemplet som du laddade ned och välj **EntityFrameworkCodeFirst.sln** att öppna exemplet. 

Om du vill köra exemplet måste du skapa tre tom databaser i Azure SQL Database:

* Shard kartan Manager-databasen
* Shard 1-databas
* Shard 2-databas

När du har skapat dessa databaser, fyller du i platshållare i **Program.cs** med din Azure SQL DB-servernamnet, databasnamnen och dina autentiseringsuppgifter för att ansluta till databaser. Skapa lösningen i Visual Studio. Visual Studio laddar ned nödvändiga NuGet-paketen för elastic database-klientbiblioteket, Entity Framework och hantering av som en del av skapandeprocessen tillfälliga fel. Se till att återställa NuGet-paket är aktiverad för din lösning. Du kan aktivera den här inställningen genom att högerklicka på lösningsfilen i Visual Studio Solution Explorer. 

## <a name="entity-framework-workflows"></a>Entity Framework arbetsflöden

Entity Framework utvecklare förlitar sig på något av följande fyra arbetsflöden att bygga program och säkerställa persistence för programobjekt:

* **Kod först (ny databas)**: EF-utvecklare skapar modellen i programkoden och sedan EF genererar databasen från den. 
* **Kod först (befintlig databas)**: Utvecklaren kan EF generera programkoden för modellen från en befintlig databas.
* **Modellera första**: Utvecklaren skapar modellen i EF designer och sedan EF skapar databasen från modellen.
* **Databasen första**: Utvecklare använder EF verktyg för att härleda modellen från en befintlig databas. 

Alla dessa metoder är beroende av DbContext-klassen för att hantera transparent databasanslutningar och databasschemat för ett program. Olika konstruktorer på basklassen DbContext gör det möjligt att olika nivåer av kontroll över relationen skapades, start av databasen och schemat skapas. Utmaningar uppstå främst från det faktum att databashantering för anslutning som tillhandahålls av EF korsar med funktioner för hantering av anslutningen databeroende routning gränssnitt som tillhandahålls av klientbiblioteket för elastiska databaser. 

## <a name="elastic-database-tools-assumptions"></a>Elastisk databas verktyg antaganden

Termdefinitioner finns [ordlista för verktyg i elastiska databaser](sql-database-elastic-scale-glossary.md).

Med elastic database-klientbiblioteket definierar du partitioner för dina programdata som kallas shardletar. Shardletar identifieras av en shardingnyckel och mappas till specifika databaser. Ett program kan ha så många databaser efter behov och distribuera shardletar för att tillhandahålla tillräckligt med kapacitet eller prestanda som har gett aktuella affärskrav. Mappningen för horisontell partitionering nyckelvärden till databaser lagras av en skärvkarta som tillhandahålls av elastic database-klientens API: er. Den här funktionen kallas **Fragmentkarthantering**, eller SMM för kort. Fragmentkartan fungerar också som den asynkrona meddelandekön till databaser för begäranden som innehåller en shardingnyckel. Den här funktionen kallas **databeroende routning**. 

Fragmentkartehanteraren skyddar användare från inkonsekvent vyer i shardlet data som kan uppstå när samtidiga shardlet hanteringsåtgärder (till exempel hur du flyttar data från en shard till en annan) sker. Om du vill göra det hanteras shard-kartor den asynkrona meddelandekön biblioteket klienten databasanslutningar för ett program. På så sätt kan funktionen fragment kartan att avsluta en databasanslutning automatiskt när fragment hanteringsåtgärder kan påverka shardlet som anslutningen har skapats för. Den här metoden måste integrera med några av EFS funktioner, till exempel skapa nya anslutningar från en befintlig för att kontrollera om databasen finns. I allmänhet har vår Fjärrvisning att standard DbContext konstruktorer bara fungerar på ett tillförlitligt sätt för stängda databasanslutningar som kan klonas på ett säkert sätt för EF fungerar. Designprincipen elastisk databas i stället är endast mäkla öppnade anslutningar. En tror att stänga en anslutning som asynkrona av klientbiblioteket innan överlämnande till EF-DbContext kan lösa problemet. Genom att stänga anslutningen och förlita dig på EF öppna den igen, kan en dock foregoes verifiering och konsekvens kontroller som utförs av biblioteket. Funktionen migreringar i EF, använder dock dessa anslutningar för att hantera underliggande databasschemat på ett sätt som är transparent för programmet. Vi rekommenderar att du behåller och kombinera alla dessa funktioner från både klientbibliotek för elastiska databaser och EF i samma program. I följande avsnitt beskrivs de här egenskaperna och kraven i detalj. 

## <a name="requirements"></a>Krav

När du arbetar med både klientbibliotek för elastiska databaser och Entity Framework-API: er som du vill behålla följande egenskaper: 

* **Skala ut**: Att lägga till eller ta bort databaser från datanivån för delat program efter behov för kapacitetskraven för programmet. Det innebär att kontroll över skapandet och borttagningen av databaser och använder karthanteraren API: er för elastiska databaser för att hantera databaser och mappningar av shardletar. 
* **Konsekvens**: Programmet använder horisontell partitionering och använder funktioner för databeroende routning av klientbiblioteket. Om du vill undvika skadade eller fel frågeresultat är om asynkrona anslutningar via fragmentkartehanteraren. Detta bevarar också verifiering och konsekvens.
* **Code första**: Att behålla praktiskt EFS kod första paradigm. I den första koden mappas klasser i programmet transparent till de underliggande strukturerna i databasen. Programkoden samverkar med DbSets som maskerar de flesta aspekter som ingår i den underliggande databasen-bearbetningen.
* **Schemat**: Entity Framework hanterar inledande databasen schemat skapas och efterföljande schemat utvecklingen via migreringar. Genom att behålla de här funktionerna, är anpassning av din app enkelt som data utvecklas. 

Följande riktlinjer anger hur uppfyller dessa krav för Code First-program med verktyg för elastiska databaser. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Databeroende routning med hjälp av EF DbContext

Databasanslutningar med Entity Framework vanligtvis hanteras via underklasser av **DbContext**. Skapa dessa underklasser av som härleds från **DbContext**. Det är här du definierar din **DbSets** som implementerar databasstödd-samlingar av CLR-objekt för ditt program. Du kan identifiera flera användbara egenskaper som inte nödvändigtvis har för andra EF code första Programscenarier i samband med databeroende routning: 

* Databasen finns redan och har registrerats i fragmentkartan för elastiska databaser. 
* Schemat för programmet har redan distribuerats till databasen (beskrivs nedan). 
* Databeroende routning anslutningar till databasen asynkrona av fragmentkartan. 

Integrera **DbContexts** med databeroende routning för skala ut:

1. Skapa fysiska databasanslutningar via klientgränssnitt elastisk databas av fragmentkartehanteraren, 
2. Omsluta anslutningen med den **DbContext** underklass
3. Skicka anslutningen till den **DbContext** basera klasser för att se till att all bearbetning på EF sida händer även. 

I följande kodexempel visas den här metoden. (Den här koden är också i det tillhörande Visual Studio-projektet)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

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
```

## <a name="main-points"></a>Huvudändamålen

* En ny konstruktor ersätter Standardkonstruktorn i DbContext-underklass 
* Konstruktorn new använder argument som krävs för databeroende Routning via klientbiblioteket för elastiska databaser:
  
  * fragmentkartan för att få åtkomst till de databeroende routning gränssnitt
  * nyckeln för horisontell partitionering för att identifiera shardlet,
  * en anslutningssträng med autentiseringsuppgifterna för databeroende routning anslutningen till fragment. 
* Anropet till Basklasskonstruktorn tar en detour till en statisk metod som utför alla steg krävs för databeroende routning. 
  
  * OpenConnectionForKey anrop av klientgränssnitt elastisk databas används på fragmentkartan för att upprätta en öppen anslutning.
  * Fragmentkartan skapas en öppen anslutning till den shard som innehåller shardlet för den angivna shardingnyckel.
  * Den här öppen anslutning skickas tillbaka till Basklasskonstruktorn av DbContext som visar att den här anslutningen är som ska användas av EF i stället för att låta EF automatiskt skapa en ny anslutning. Det här sättet anslutningen har taggats av elastic database-klientens API så att den kan garantera konsekvens under fragment kartan hanteringsåtgärder.

Använd nya konstruktorn för din DbContext-underklass i stället för Standardkonstruktorn i din kod. Här är ett exempel: 

```csharp
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
```

Nya konstruktorn öppnar anslutningen till den shard som innehåller data för shardlet som identifieras av värdet för **tenantid1**. Koden i den **med** block förblir oförändrat för att komma åt den **DbSet** för bloggar med EF på fragment för **tenantid1**. Detta ändrar semantik för koden i med blockera, till exempel alla databasåtgärder har nu begränsats till en shard där **tenantid1** sparas. Exempelvis kan en LINQ-fråga över bloggarna **DbSet** bara returnera bloggar som lagras på den aktuella sharden, men inte de som finns på andra shards.  

#### <a name="transient-faults-handling"></a>Tillfälliga fel som hanterar

Microsoft Patterns & Practices-teamet publicerade den [The hantering av Programblocket tillfälliga](https://msdn.microsoft.com/library/dn440719.aspx). Biblioteket används med Elastiskt skalat klientbibliotek i kombination med EF. Se dock till att det returnerar alla tillfälligt undantag till en plats där du kan se till att den nya konstruktorn används efter ett tillfälligt fel så att alla nya anslutningsförsök görs med hjälp av konstruktorer du tweaked. I annat fall en anslutning till rätt fragment är inte säkert och det finns inga garantier som anslutningen bibehålls vid ändringar till i fragmentkartan. 

Följande kodexempel visar hur du kan använda en SQL-återförsöksprincip runt den nya **DbContext** underklasskonstruktorer: 

```csharp
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
```

**SqlDatabaseUtils.SqlRetryPolicy** i koden ovan definieras som en **SqlDatabaseTransientErrorDetectionStrategy** med ett återförsöksvärde på 10 och 5 sekunder väntetid mellan återförsök. Den här metoden liknar vägledning för EF och användarinitierad transaktioner (se [begränsningar i Körningsstrategier (EF6 och senare)](https://msdn.microsoft.com/data/dn307226). Båda situationer kräver att programmet styr omfattningen som tillfälligt undantag returnerar: antingen öppna transaktionen eller återskapa kontext från rätt konstruktorn (som du såg) som använder klientbiblioteket för elastiska databaser.

Behovet av att styra där tillfälliga undantag ta oss tillbaka i omfånget utesluter även användningen av inbyggt **SqlAzureExecutionStrategy** som medföljer EF. **SqlAzureExecutionStrategy** ska öppna en anslutning utan inte använda **OpenConnectionForKey** och därför kringgå verifiering som utförs som en del av den **OpenConnectionForKey**anropa. I stället kodexemplet använder inbyggt **DefaultExecutionStrategy** som också medföljer EF. Inte **SqlAzureExecutionStrategy**, den fungerar korrekt i kombination med återförsöksprincipen från hantering av tillfälliga fel. Körningsprincipen har angetts i den **ElasticScaleDbConfiguration** klass. Observera att vi inte valt att använda **DefaultSqlExecutionStrategy** eftersom den föreslår med **SqlAzureExecutionStrategy** om tillfälliga undantag inträffar - som skulle leda till fel beteende enligt beskrivningen. Läs mer om olika återförsöksprinciper och EF [Anslutningsåterhämtning i EF](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Konstruktorn omskrivningar

Kodexemplen ovan illustrerar de standard-konstruktorn skriver krävs för ditt program för att kunna använda databeroende routning med Entity Framework. I följande tabell generaliserar den här metoden till andra konstruktorer. 

| Aktuella konstruktor | Ny konstruktorn för data | Grundläggande konstruktor | Anteckningar |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, bool) |Anslutningen måste vara en funktion av fragmentkartan och nyckeln databeroende routning. Du måste kringgå automatisk anslutning skapandet av EF och i stället använda fragmentkartan mäkla anslutningen. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, bool) |Anslutningen är en funktion av fragmentkartan och nyckeln databeroende routning. En fasta databasrollen namn eller anslutningssträng fungerar inte som de kringgå verifiering av fragmentkartan. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Anslutningen skapades för angivna kartan och horisontell partitionering shardnyckeln med modellen tillhandahålls. Den kompilerade modellen överförs till den grundläggande c'tor. |
| MyContext(DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |Anslutningen måste härledas från fragmentkartan och nyckeln. Det går inte att ange indata (såvida inte dessa indata har redan använder fragmentkartan och nyckeln). Resultatet skickas vidare. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Anslutningen måste härledas från fragmentkartan och nyckeln. Det går inte att ange indata (såvida inte dessa indata använde fragmentkartan och nyckeln). Den kompilerade modellen skickas vidare. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |Konstruktorn new måste se till att alla anslutningar i ObjectContext skickas som en inmatning är riktas om till en anslutning som hanteras av Elastic Scale. En detaljerad beskrivning av ObjectContexts ligger utanför omfånget för det här dokumentet. |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |Anslutningen måste härledas från fragmentkartan och nyckeln. Anslutningen kan inte anges som indata (om inte dessa indata har redan använder fragmentkartan och nyckeln). Modellen och booleskt värde som överförs till konstruktorn basklass. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Shard schemadistributionen via EF-migrering

Automatisk schemahantering är att underlätta tillhandahålls av Entity Framework. I samband med program med hjälp av verktyg för elastiska databaser som du vill behålla den här funktionen att automatiskt etablera schemat till nyligen skapade shards när databaserna läggs till delat program. I första hand är att öka kapaciteten på datanivån för fragmenterade (sharded) program som använder EF. Förlita dig på EFS funktioner för schemahantering minskar enklare för administration av databasen med ett delat program som bygger på EF. 

Schemadistributionen via EF-migrering fungerar bäst på **inte öppnats anslutningar**. Detta skiljer sig från det scenariot för databeroende routning som förlitar sig på öppnade anslutningen tillhandahålls av elastic database-klientens API. En annan skillnaden är konsekvens-krav: När önskvärt att garantera konsekvens för alla databeroende routning anslutningar att skydda mot manipulering av samtidiga fragment kartan, är det inte ett problem med ursprungliga schema-distribution till en ny databas som har ännu inte har registrerats i fragmentkartan och inte ännu allokerats för att lagra shardletar. Du kan därför förlitar sig på vanlig databasanslutningar för det här scenariot, till skillnad från databeroende routning.  

Detta leder till en metod där schemadistributionen via EF-migrering är direkt kopplade till registreringen av den nya databasen som ett fragment i programmets fragmentkartan. Detta är beroende av följande krav: 

* Databasen har redan skapats. 
* Databasen är tom – den innehåller inga användarschemat och inga användardata.
* Databasen kan ännu inte nås via elastisk databas-klientens API: er för databeroende routning. 

Dessa krav är uppfyllda, kan du skapa en vanlig icke öppnade **SqlConnection** inledningsanförande EF-migrering för distribution av schemat. Följande kodexempel illustrerar den här metoden. 

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema 

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
```

Det här exemplet visar metoden **RegisterNewShard** som registrerar fragmentet i fragmentkartan, distribuerar schemat via EF-migrering och lagrar en mappning av en shardingnyckel till fragment. Den förlitar sig på en konstruktor med den **DbContext** underklass (**ElasticScaleContext** i det här exemplet) som tar en SQL-anslutningssträng som indata. Koden för den här konstruktorn är enkel och tydlig, som i följande exempel visas: 

```csharp
// C'tor to deploy schema and migrations to a new shard 
protected internal ElasticScaleContext(string connectionString) 
    : base(SetInitializerForConnection(connectionString)) 
{ 
} 

// Only static methods are allowed in calls into base class c'tors 
private static string SetInitializerForConnection(string connectionString) 
{ 
    // You want existence checks so that the schema can get deployed 
    Database.SetInitializer<ElasticScaleContext<T>>( 
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

    return connectionString; 
} 
```

En kan ha använt versionen av konstruktorn ärvd från basklassen. Men koden måste se till att standard-initieraren för EF används när du ansluter. Kortsiktiga detour därför i den statiska metoden innan du anropar i Basklasskonstruktorn med anslutningssträngen. Observera att registreringen av fragment ska köras i en annan programdomän eller process för att se till att inställningarna för EF initieraren inte står i konflikt. 

## <a name="limitations"></a>Begränsningar

De metoder som beskrivs i det här dokumentet medföra några begränsningar: 

* EF-program som använder **LocalDb** först måste du migrera till en vanlig SQL Server-databas innan du använder klientbiblioteket för elastiska databaser. Skala ut ett program via horisontell partitionering med Elastic Scale är inte möjligt med **LocalDb**. Observera att utveckling kan fortfarande använda **LocalDb**. 
* Alla ändringar i programmet som implicerar schemaändringar för databasen måste du gå igenom EF-migrering på alla shards. Exempelkod för det här dokumentet visar inte hur du gör detta. Överväg att använda Update-Database med en ConnectionString-parameter för att iterera över alla shards; eller extrahera T-SQL-skript för väntande migreringen med hjälp av Update-Database med skriptet för - alternativet och tillämpa T-SQL-skript på din shards.  
* Med en begäran kan antas det att alla dess databasbearbetning ingår i en enda shard som identifieras av shardingnyckel som anges i begäran. Men innehåller här antagandet inte alltid SANT. Till exempel när det går inte att göra en shardingnyckel som är tillgängliga. För att åtgärda det här klientbiblioteket ger den **MultiShardQuery** klass som implementerar en anslutning abstraktion för att fråga över flera fragment. Lära sig använda den **MultiShardQuery** i kombination med EF ligger utanför omfånget för det här dokumentet

## <a name="conclusion"></a>Sammanfattning

Genom att följa stegen i det här dokumentet, EF-program kan använda funktionen för elastic database-klientbiblioteket för databeroende routning av omstrukturering konstruktorerna i den **DbContext** underklasser som används i EF-programmet. Detta begränsar de ändringar som krävs för att de platserna där **DbContext** klasser finns redan. EF-program kan dessutom fortsätta att dra nytta av automatisk schemadistributionen genom att kombinera de steg som anropar nödvändiga EF-migrering med registreringen av nya fragmenten och mappningar i fragmentkartan. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
