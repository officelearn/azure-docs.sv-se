---
title: Använda elastiskt databasklientbibliotek med Entity Framework
description: Använda klientbibliotek för elastisk databas och entitetsramverk för kodning av databaser
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 1653a904875964d86864c59c718603a6dacdcbda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087184"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Klientbibliotek för elastisk databas med entitetsramverk

Det här dokumentet visar ändringarna i ett entitetsramverksprogram som behövs för att integreras med [verktygen för elastisk databas](sql-database-elastic-scale-introduction.md). Fokus ligger på att komponera [fragmentkarthantering](sql-database-elastic-scale-shard-map-management.md) och [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md) med metoden Entity Framework **Code First.** Kod [först - Ny databas](https://msdn.microsoft.com/data/jj193542.aspx) handledning för EF fungerar som kör exempel i hela detta dokument. Exempelkoden som medföljer det här dokumentet är en del av elastiska databasverktygs uppsättning exempel i Visual Studio-kodexemplen.

## <a name="downloading-and-running-the-sample-code"></a>Hämta och köra exempelkoden

Så här hämtar du koden för den här artikeln:

* Visual Studio 2012 eller senare krävs. 
* Hämta [exemplet Elastiska DB-verktyg för Azure SQL - Entity Framework Integration](https://github.com/Azure/elastic-db-tools/). Packa upp provet till en plats som du väljer.
* Starta Visual Studio. 
* Välj Arkiv -> Öppna projekt/lösning i Visual Studio. 
* I dialogrutan **Öppna projekt** navigerar du till det exempel som du hämtade och väljer **EntityFrameworkCodeFirst.sln** för att öppna exemplet. 

Om du vill köra exemplet måste du skapa tre tomma databaser i Azure SQL Database:

* Databas för Fragment Karthanteraren
* Shard 1 databas
* Shard 2 databas

När du har skapat dessa databaser fyller du i platsinnehavare i **Program.cs** med ditt Azure SQL DB-servernamn, databasnamnen och dina autentiseringsuppgifter för att ansluta till databaserna. Bygg lösningen i Visual Studio. Visual Studio hämtar de nödvändiga NuGet-paketen för klientbiblioteket för elastiska databaser, Entitetsramverk och hantering av tillfälliga fel som en del av byggprocessen. Se till att återställa NuGet-paket är aktiverat för din lösning. Du kan aktivera den här inställningen genom att högerklicka på lösningsfilen i Utforskaren för Visual Studio-lösning. 

## <a name="entity-framework-workflows"></a>Entitetsramverksarbetsflöden

Entity Framework-utvecklare förlitar sig på något av följande fyra arbetsflöden för att skapa program och för att säkerställa persistens för programobjekt:

* **Kod först (ny databas)**: EF-utvecklaren skapar modellen i programkoden och sedan genererar EF databasen från den. 
* **Kod först (befintlig databas)**: Utvecklaren låter EF generera programkoden för modellen från en befintlig databas.
* **Modell först:** Utvecklaren skapar modellen i EF-designern och sedan skapar EF databasen från modellen.
* **Databas Först:** Utvecklaren använder EF-verktyg för att härleda modellen från en befintlig databas. 

Alla dessa metoder är beroende av klassen DbContext för att transparent hantera databasanslutningar och databasschema för ett program. Olika konstruktorer i basklassen DbContext ger olika nivåer av kontroll över skapande av anslutningar, databas bootstrapping och schemaskapande. Utmaningar beror främst på att databasanslutningshanteringen som tillhandahålls av EF skär med anslutningshanteringsfunktionerna i de databeroende routningsgränssnitten som tillhandahålls av klientbiblioteket för elastisk databas. 

## <a name="elastic-database-tools-assumptions"></a>Antaganden om elastiska databasverktyg

För termdefinitioner finns i [Ordlistan för elastiska databasverktyg](sql-database-elastic-scale-glossary.md).

Med elastiskt databasklientbibliotek definierar du partitioner av programdata som kallas shardlets. Shardlets identifieras med en sharding-nyckel och mappas till specifika databaser. Ett program kan ha så många databaser som behövs och distribuera shardlets för att ge tillräckligt med kapacitet eller prestanda med tanke på aktuella affärskrav. Mappningen av sharding nyckelvärden till databaserna lagras av en fragmentkarta som tillhandahålls av den elastiska databasen klient API: er. Den här funktionen kallas **Shard Map Management**eller SMM för korta. Fragmentkartan fungerar också som mäklare av databasanslutningar för begäranden som har en sharding-nyckel. Den här funktionen kallas **databeroende routning**. 

Fragmentkarthanteraren skyddar användare från inkonsekventa vyer till fragmentdata som kan uppstå när samtidiga shardlethanteringsåtgärder (till exempel att flytta data från en fragment till en annan) sker. För att göra det, shard kartor som hanteras av klientbiblioteket mäklare databasanslutningar för ett program. På så sätt kan fragmentkartfunktionen automatiskt döda en databasanslutning när fragmenthanteringsåtgärder kan påverka fragmentet som anslutningen har skapats för. Den här metoden måste integreras med några av EF:s funktioner, till exempel att skapa nya anslutningar från en befintlig för att söka efter databasexistens. I allmänhet har vår iakttagelse varit att standard DbContext konstruktörer endast fungerar tillförlitligt för slutna databasanslutningar som säkert kan klonas för EF arbete. Designprincipen för elastisk databas är istället att endast förmedla öppnade anslutningar. Man kan tro att stänga en anslutning förmedlas av klientbiblioteket innan du lämnar över den till EF DbContext kan lösa detta problem. Men genom att stänga anslutningen och förlita sig på EF för att öppna den igen, avstår man från validering och konsekvenskontroller som utförs av biblioteket. Migreringsfunktionen i EF använder dock dessa anslutningar för att hantera det underliggande databasschemat på ett sätt som är transparent för programmet. Helst behåller och kombinerar du alla dessa funktioner från både det elastiska databasklientbiblioteket och EF i samma program. I följande avsnitt beskrivs dessa egenskaper och krav mer i detalj. 

## <a name="requirements"></a>Krav

När du arbetar med både det elastiska databasklientbiblioteket och API:er för entitetsramverk vill du behålla följande egenskaper: 

* **Utskalning**: Om du vill lägga till eller ta bort databaser från datanivån för det fragmenterade programmet efter behov för programmets kapacitetskrav. Det innebär kontroll över skapandet och borttagningen av databaser och användning av API:er för elastiska databaser shard map manager för att hantera databaser och mappningar av shardlets. 
* **Konsekvens**: Programmet använder sharding och använder de databeroende routningsfunktionerna i klientbiblioteket. För att undvika skador eller felaktiga frågeresultat förmedlas anslutningar via fragmentkarthanteraren. Detta behåller också validering och konsekvens.
* **Kod först:** Att behålla bekvämligheten med EF: s kod första paradigm. I Kod först mappas klasser i programmet transparent till de underliggande databasstrukturerna. Programkoden interagerar med DbSets som maskerar de flesta aspekter som är involverade i den underliggande databasbearbetningen.
* **Schema**: Entity Framework hanterar inledande skapande av databasschema och efterföljande schemautveckling genom migreringar. Genom att behålla dessa funktioner är det enkelt att anpassa appen när data utvecklas. 

Följande vägledning instruerar hur du uppfyller dessa krav för Kod först-program med hjälp av elastiska databasverktyg. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Databeroende routning med EF DbContext

Databasanslutningar med Entity Framework hanteras vanligtvis via underklasser till **DbContext**. Skapa dessa underklasser genom att härleda från **DbContext**. Här definierar du dina **DbSets** som implementerar databasstödda samlingar av CLR-objekt för ditt program. I samband med databeroende routning kan du identifiera flera användbara egenskaper som inte nödvändigtvis finns för andra EF-kod första programscenarier: 

* Databasen finns redan och har registrerats i den elastiska databasshard-kartan. 
* Schemat för programmet har redan distribuerats till databasen (förklaras nedan). 
* Databeroende routningsanslutningar till databasen förmedlas av fragmentkartan. 

Så här integrerar du **DbContexts** med databeroende routning för utskalning:

1. Skapa fysiska databasanslutningar via klientgränssnitten för elastisk databas i fragmentkarthanteraren. 
2. Radbryt anslutningen med **underklassen DbContext**
3. Skicka anslutningen ner till **DbContext** basklasser för att säkerställa att all bearbetning på EF-sidan händer också. 

Följande kodexempel illustrerar den här metoden. (Denna kod finns också i det medföljande Visual Studio-projektet)

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

## <a name="main-points"></a>Huvudpunkter

* En ny konstruktor ersätter standardkonstruktorn i underklassen DbContext 
* Den nya konstruktorn tar de argument som krävs för databeroende routning via elastiskt databasklientbibliotek:
  
  * fragmentkartan för att komma åt de databeroende routningsgränssnitten,
  * skärpningsnyckeln för att identifiera shardlet,
  * en anslutningssträng med autentiseringsuppgifterna för den databeroende routningsanslutningen till sharden. 
* Anropet till basklasskonstruktorn tar en omväg till en statisk metod som utför alla steg som krävs för databeroende routning. 
  
  * Den använder OpenConnectionForKey-anropet för klientgränssnitten för elastisk databas på fragmentkartan för att upprätta en öppen anslutning.
  * Fragmentkartan skapar den öppna anslutningen till shard som innehåller fragmentet för den angivna sharding-nyckeln.
  * Den här öppna anslutningen skickas tillbaka till basklasskonstruktorn för DbContext för att ange att den här anslutningen ska användas av EF i stället för att låta EF skapa en ny anslutning automatiskt. På så sätt har anslutningen taggats av den elastiska databasklient-API:et så att den kan garantera konsekvens under fragmentkarthanteringsåtgärder.

Använd den nya konstruktorn för dbContext-underklassen i stället för standardkonstruktorn i koden. Här är ett exempel: 

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

Den nya konstruktorn öppnar anslutningen till shard som innehåller data för shardlet som identifieras av värdet **för tenantid1**. Koden i **det använda blocket** förblir oförändrad för att komma åt **DbSet** för bloggar som använder EF på fragmentet för **tenantid1**. Detta ändrar semantik för koden i det använda blocket så att alla databasåtgärder nu begränsas till den fragment där **tenantid1** hålls. Till exempel skulle en LINQ fråga över bloggar **DbSet** bara returnera bloggar lagras på den aktuella fragmentet, men inte de som lagras på andra shards.  

#### <a name="transient-faults-handling"></a>Hantering av tillfälliga fel

Teamet Microsoft Patterns & Practices publicerade [programblocket för transientfelhantering](https://msdn.microsoft.com/library/dn440719.aspx). Biblioteket används med elastisk skala klientbibliotek i kombination med EF. Se dock till att alla tillfälliga undantag återgår till en plats där du kan se till att den nya konstruktorn används efter ett tillfälligt fel så att alla nya anslutningsförsök görs med hjälp av konstruktörerna som du har justerat. Annars garanteras inte en anslutning till rätt shard, och det finns inga garantier för att anslutningen upprätthålls när ändringar i fragmentkartan sker. 

Följande kodexempel illustrerar hur en SQL-återförsöksprincip kan användas runt de nya dbContext-underklasskonstruktörerna: **DbContext** 

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

**SqlDatabaseUtils.SqlRetryPolicy** i koden ovan definieras som en **SqlDatabaseTransientErrorDetectionStrategy** med ett antal försök på 10 och 5 sekunders väntetid mellan återförsök. Detta tillvägagångssätt liknar vägledningen för EF-transaktioner och användarinitierade transaktioner (se [Begränsningar med återförsökskörningsstrategier (EF6 och framåt).](https://msdn.microsoft.com/data/dn307226) Båda situationerna kräver att programprogrammet styr det omfång som det tillfälliga undantaget returnerar: att antingen öppna transaktionen igen eller (som visad) återskapa kontexten från rätt konstruktor som använder klientbiblioteket för elastisk databas.

Behovet av att kontrollera var tillfälliga undantag tar oss tillbaka i omfattning utesluter också användningen av den inbyggda **SqlAzureExecutionStrategy** som följer med EF. **SqlAzureExecutionStrategy** skulle öppna en anslutning igen men inte använda **OpenConnectionForKey** och därför kringgå all validering som utförs som en del av **OpenConnectionForKey-anropet.** I stället använder kodexemplet den inbyggda **DefaultExecutionStrategy** som också levereras med EF. I motsats till **SqlAzureExecutionStrategy**fungerar den korrekt i kombination med återförsöksprincipen från Transient Fault Handling. Körningsprincipen anges i klassen **ElasticScaleDbConfiguration.** Observera att vi beslutat att inte använda **DefaultSqlExecutionStrategy** eftersom det föreslår att du använder **SqlAzureExecutionStrategy** om tillfälliga undantag inträffar - vilket skulle leda till fel beteende som diskuteras. Mer information om de olika principerna för återförsök och EF finns [i Anslutningsåtersåterkoppling i EF](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Konstruktör skriver om

Kodexemplen ovan illustrerar standardkonstruktorrea-skrivningar som krävs för ditt program för att använda databeroende routning med entitetsramverket. Följande tabell generaliserar den här metoden till andra konstruktörer. 

| Nuvarande konstruktor | Omskriven konstruktor för data | Baskonstruktor | Anteckningar |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |Anslutningen måste vara en funktion av fragmentmappningen och den databeroende routningsnyckeln. Du måste by-pass automatisk anslutning skapas av EF och istället använda fragment kartan för att mäkla anslutningen. |
| MyContext(sträng) |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |Anslutningen är en funktion av fragmentkartan och den databeroende routningsnyckeln. Ett fast databasnamn eller en fast databassträng fungerar inte eftersom de genomströmningsvalidering av fragmentkartan. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Anslutningen skapas för den angivna fragmentkartan och sharding-tangenten med den modell som tillhandahålls. Den kompilerade modellen vidarebefordras till basen c'tor. |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |Anslutningen måste härledas från fragmentkartan och nyckeln. Det kan inte tillhandahållas som en ingång (om inte den ingången redan använde fragmentkartan och nyckeln). Booleskerna förs vidare. |
| MyContext(sträng, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Anslutningen måste härledas från fragmentkartan och nyckeln. Det kan inte tillhandahållas som en ingång (om inte indata använde fragmentkartan och nyckeln). Den kompilerade modellen vidarebefordras. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |Den nya konstruktorn måste se till att alla anslutningar i ObjectContext som skickas som en indata omdirigeras till en anslutning som hanteras av elastisk skala. En detaljerad diskussion om ObjectContexts ligger utanför det här dokumentets omfattning. |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |Anslutningen måste härledas från fragmentkartan och nyckeln. Anslutningen kan inte tillhandahållas som en ingång (om inte indata redan använde fragmentkartan och nyckeln). Modell och boolesk förs vidare till basklasskonstruktorn. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Fragmentschemadistribution via EF-migreringar

Automatisk schemahantering är en bekvämlighet som tillhandahålls av entity framework. I samband med program som använder elastiska databasverktyg vill du behålla den här funktionen för att automatiskt etablera schemat till nyskapade shards när databaser läggs till i det fragmenterade programmet. Det primära användningsfallet är att öka kapaciteten på datanivån för fragmenterade program med EF. Om du förlitar dig på EF:s funktioner för schemahantering minskar databasadministrationsansträngningen med ett fragmenterat program som bygger på EF. 

Schemadistribution via EF-migreringar fungerar bäst på **oöppnade anslutningar**. Detta står i kontrast till scenariot för databeroende routning som är beroende av den öppnade anslutningen som tillhandahålls av den elastiska databasklient-API:et. En annan skillnad är konsekvenskravet: Även om det är önskvärt att säkerställa konsekvens för alla databeroende routningsanslutningar för att skydda mot samtidig fragmentkartmanipulering, är det inte ett problem med inledande schemadistribution till en ny databas som har ännu inte registrerats i fragmentkartan och ännu inte tilldelats för att hålla shardlets. Du kan därför förlita dig på vanliga databasanslutningar för det här scenariot, i motsats till databeroende routning.  

Detta leder till en metod där schemadistribution via EF-migreringar är tätt kopplad till registreringen av den nya databasen som en fragment i programmets fragmentkarta. Detta bygger på följande förutsättningar: 

* Databasen har redan skapats. 
* Databasen är tom - den innehåller inget användarschema och inga användardata.
* Databasen kan ännu inte nås via api:erna för elastiska databasklienter för databeroende routning. 

Med dessa förutsättningar på plats kan du skapa en vanlig oöppnad **SqlConnection** för att starta EF-migreringar för schemadistribution. Följande kodexempel illustrerar den här metoden. 

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

Det här exemplet visar metoden **RegisterNewShard** som registrerar fragmentet i fragmentkartan, distribuerar schemat via EF-migreringar och lagrar en mappning av en fragmentnyckel till fragmentet. Den är beroende av en konstruktör av **dbContext-underklassen** (**ElasticScaleContext** i exemplet) som tar en SQL-anslutningssträng som indata. Koden för den här konstruktorn är rättfram, vilket visas i följande exempel: 

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

Man kan ha använt den version av konstruktorn som ärvts från basklassen. Men koden måste se till att standardinitieraren för EF används vid anslutning. Därav den korta omvägen till den statiska metoden innan du ringer in i basklasskonstruktorn med anslutningssträngen. Observera att registreringen av shards ska köras i en annan appdomän eller process för att säkerställa att initialiseringsinställningarna för EF inte står i konflikt. 

## <a name="limitations"></a>Begränsningar

De tillvägagångssätt som beskrivs i detta dokument medför ett par begränsningar: 

* EF-program som använder **LocalDb** måste först migrera till en vanlig SQL Server-databas innan du använder elastiskt databasklientbibliotek. Det går inte att skala ut ett program genom sharding med elastisk skala med **LocalDb**. Observera att utveckling fortfarande kan använda **LocalDb**. 
* Alla ändringar i programmet som innebär ändringar i databasschemat måste gå igenom EF-migreringar på alla shards. Exempelkoden för det här dokumentet visar inte hur du gör detta. Överväg att använda Update-Database med en ConnectionString-parameter för att iterera över alla shards. eller extrahera T-SQL-skriptet för den väntande migreringen med alternativet Update-Database med alternativet -Script och tillämpa T-SQL-skriptet på dina shards.  
* Med en begäran antas det att all dess databasbearbetning finns i en enda shard som identifieras av sharding-nyckeln som tillhandahålls av begäran. Detta antagande stämmer dock inte alltid. Till exempel när det inte är möjligt att göra en sharding nyckel tillgänglig. För att åtgärda detta tillhandahåller klientbiblioteket klassen **MultiShardQuery** som implementerar en anslutningsabstraktion för att fråga över flera shards. Att lära sig att använda **MultiShardQuery** i kombination med EF ligger utanför detta dokuments räckvidd

## <a name="conclusion"></a>Slutsats

Genom de steg som beskrivs i det här dokumentet kan EF-program använda det elastiska databasklientbibliotekets kapacitet för databeroende routning genom att omfactorera konstruktörer av **dbContext-underklasserna** som används i EF-programmet. Detta begränsar de ändringar som krävs till de platser där **DbContext-klasser** redan finns. Dessutom kan EF-program fortsätta att dra nytta av automatisk schemadistribution genom att kombinera de steg som anropar nödvändiga EF-migreringar med registrering av nya shards och mappningar i fragmentkartan. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
