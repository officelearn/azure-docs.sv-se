---
title: Använda elastiskt databasklientbibliotek med Dapper
description: Använda elastiskt databasklientbibliotek med Dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 83d24d45d7628a2e02068c8757fa6568d6d3fc37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823481"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Använda elastiskt databasklientbibliotek med Dapper
Det här dokumentet är till för utvecklare som förlitar sig på Dapper för att skapa program, men också vill anamma [elastiska databasverktyg](sql-database-elastic-scale-introduction.md) för att skapa program som implementerar fragmentering för att skala ut sin datanivå.  Det här dokumentet illustrerar de ändringar i Dapper-baserade program som är nödvändiga för att integrera med elastiska databasverktyg. Vårt fokus ligger på att komponera den elastiska databasshardhanteringen och databeroende routing med Dapper. 

**Exempelkod:** [Elastiska databasverktyg för Azure SQL Database - Dapper-integrering](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Det är enkelt att integrera **Dapper** och **DapperExtensions** med klientbiblioteket för elastiska databaser för Azure SQL Database. Dina program kan använda databeroende routning genom att ändra skapandet och öppnandet av nya [SqlConnection-objekt](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) för att använda [OpenConnectionForKey-anropet](https://msdn.microsoft.com/library/azure/dn807226.aspx) från [klientbiblioteket](https://msdn.microsoft.com/library/azure/dn765902.aspx). Detta begränsar ändringar i ditt program till att endast där nya anslutningar skapas och öppnas. 

## <a name="dapper-overview"></a>Dapper översikt
**Dapper** är en objekt-relationell mapper. Den mappar .NET-objekt från ditt program till en relationsdatabas (och vice versa). Den första delen av exempelkoden illustrerar hur du kan integrera klientbiblioteket för elastiska databaser med Dapper-baserade program. Den andra delen av exempelkoden illustrerar hur du integrerar när du använder både Dapper och DapperExtensions.  

Mapperfunktionen i Dapper innehåller tilläggsmetoder för databasanslutningar som förenklar inlämning av T-SQL-uttryck för körning eller frågar databasen. Dapper gör det till exempel enkelt att mappa mellan .NET-objekten och parametrarna för SQL-uttryck för Kör-anrop, eller att använda resultaten av dina SQL-frågor till .NET-objekt med **frågeanrop** från Dapper. **Execute** 

När du använder DapperExtensions behöver du inte längre ange SQL-satser. Tilläggsmetoder som **GetList** eller **Infoga** över databasanslutningen skapar SQL-uttrycken bakom kulisserna.

En annan fördel med Dapper och även DapperExtensions är att programmet styr skapandet av databasanslutningen. Detta hjälper till att interagera med den elastiska databasen klientbibliotek som mäklare databasanslutningar baserat på mappning av shardlets till databaser.

För att få Dapper församlingar, se [Dapper punkt netto](https://www.nuget.org/packages/Dapper/). För Dapper-tilläggen, se [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>En snabb titt på den elastiska databasen klientbibliotek
Med klientbiblioteket för elastisk databas definierar du partitioner av programdata som kallas *shardletar,* mappar dem till databaser och identifierar dem genom *att sharding keys*. Du kan ha så många databaser som du behöver och distribuera dina shardlets över dessa databaser. Mappningen av sharding nyckelvärden till databaserna lagras av en fragmentkarta som tillhandahålls av bibliotekets API: er. Den här funktionen kallas **shard map management**. Fragmentkartan fungerar också som mäklare av databasanslutningar för begäranden som har en sharding-nyckel. Den här funktionen kallas **databeroende routning**.

![Fragmentkartor och databeroende routning][1]

Fragmentkarthanteraren skyddar användare från inkonsekventa vyer till fragmentdata som kan uppstå när samtidiga shardlethanteringsåtgärder pågår i databaserna. För att göra det, shard kartor mäklare databasanslutningar för ett program som skapats med biblioteket. När fragmenthanteringsåtgärder kan påverka fragmentet gör detta att fragmentkartfunktionen automatiskt kan döda en databasanslutning. 

I stället för att använda det traditionella sättet att skapa anslutningar för Dapper måste du använda [metoden OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn824099.aspx). Detta säkerställer att all validering sker och anslutningar hanteras korrekt när data flyttas mellan shards.

### <a name="requirements-for-dapper-integration"></a>Krav för Dapper integration
När du arbetar med både det elastiska databasklientbiblioteket och Dapper-API:erna vill du behålla följande egenskaper:

* **Skala ut**: Vi vill lägga till eller ta bort databaser från datanivån för det fragmenterade programmet efter behov för programmets kapacitetskrav. 
* **Konsekvens:** Eftersom programmet skalas ut med hjälp av fragmentering måste du utföra databeroende routning. Vi vill använda bibliotekets databeroende routningsfunktioner för att göra det. I synnerhet vill du behålla de validerings- och konsekvensgarantier som tillhandahålls av anslutningar som förmedlas via fragmentkarthanteraren för att undvika skador eller felaktiga frågeresultat. Detta säkerställer att anslutningar till en viss shardlet avvisas eller stoppas om (till exempel) shardlet för närvarande flyttas till en annan shard med hjälp av Split/Merge API:er.
* **Objektmappning:** Vi vill behålla bekvämligheten med de mappningar som Dapper tillhandahåller för att översätta mellan klasser i programmet och de underliggande databasstrukturerna. 

Följande avsnitt innehåller vägledning för dessa krav för applikationer baserade på **Dapper** och **DapperExtensions**.

## <a name="technical-guidance"></a>Teknisk vägledning
### <a name="data-dependent-routing-with-dapper"></a>Databeroende routning med Dapper
Med Dapper är programmet vanligtvis ansvarigt för att skapa och öppna anslutningarna till den underliggande databasen. Med tanke på en typ T av programmet returnerar Dapper frågeresultat som .NET-samlingar av typen T. Dapper utför mappningen från T-SQL-resultatraderna till objekten av typen T. På samma sätt mappar Dapper .NET-objekt till SQL-värden eller parametrar för DML-satser (Data manipulation language). Dapper erbjuder den här funktionen via tilläggsmetoder på det vanliga [SqlConnection-objektet](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) från ADO .NET SQL Client-biblioteken. SQL-anslutningen som returneras av API:erna för elastisk skala för DDR är också vanliga [SqlConnection-objekt.](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) Detta gör det möjligt för oss att direkt använda Dapper-tillägg över den typ som returneras av klientbibliotekets DDR API, eftersom det också är en enkel SQL Client-anslutning.

Dessa observationer gör det enkelt att använda anslutningar som förmedlas av den elastiska databasen klientbibliotek för Dapper.

Det här kodexemplet (från det medföljande exemplet) illustrerar metoden där sharding-nyckeln tillhandahålls av programmet till biblioteket för att förmedla anslutningen till rätt fragment.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1,
                     connectionString: connStrBldr.ConnectionString,
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

Anropet till [OpenConnectionForKey-API:et](https://msdn.microsoft.com/library/azure/dn807226.aspx) ersätter standardskapandet och öppnandet av en SQL-klientanslutning. [OpenConnectionForKey-anropet](https://msdn.microsoft.com/library/azure/dn807226.aspx) tar de argument som krävs för databeroende routning: 

* Fragmentkartan för åtkomst till databeroende routningsgränssnitt
* Skärva nyckeln för att identifiera shardlet
* Autentiseringsuppgifterna (användarnamn och lösenord) för att ansluta till shard

Fragmentmappningsobjektet skapar en anslutning till fragmentet som innehåller fragmentet för den angivna sharding-nyckeln. Api:erna för elastiska databasklienter taggar också anslutningen för att implementera dess konsekvensgarantier. Eftersom anropet till [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) returnerar ett vanligt SQL-klientanslutningsobjekt följer det efterföljande anropet till metoden **Kör** tillägg från Dapper standardpraxisen Dapper.

Frågor fungerar ungefär på samma sätt - du öppnar först anslutningen med [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) från klient-API: et. Sedan använder du de vanliga Dapper-tilläggsmetoderna för att mappa resultatet av SQL-frågan till .NET-objekt:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate ))
    {
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT *
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Observera att **det använda** blocket med DDR-anslutningen omfattar alla databasåtgärder i blocket till den fragment där tenantId1 behålls. Frågan returnerar bara bloggar som lagras på den aktuella fragmentet, men inte de som lagras på andra shards. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Databeroende routning med Dapper och DapperExtensions
Dapper levereras med ett ekosystem av ytterligare tillägg som kan ge ytterligare bekvämlighet och abstraktion från databasen när du utvecklar databasapplikationer. DapperExtensions är ett exempel. 

Att använda DapperExtensions i ditt program ändrar inte hur databasanslutningar skapas och hanteras. Det är fortfarande programmets ansvar att öppna anslutningar och vanliga SQL Client-anslutningsobjekt förväntas av tilläggsmetoderna. Vi kan lita på [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) som beskrivs ovan. Som följande kodexempel visar är den enda ändringen att du inte längre behöver skriva T-SQL-uttrycken:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Och här är kodexemplet för frågan: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Hantering av tillfälliga fel
Microsoft Patterns & Practices-teamet publicerade [Transient Fault Handling Application Block](https://msdn.microsoft.com/library/hh680934.aspx) för att hjälpa programutvecklare att minska vanliga tillfälliga feltillstånd som uppstår när de körs i molnet. För mer information, se [Uthållighet, Secret of All Triumphs: Använda Transient Fault Handling Application Block](https://msdn.microsoft.com/library/dn440719.aspx).

Kodexemplet är beroende av det tillfälliga felbiblioteket för att skydda mot tillfälliga fel. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** i koden ovan definieras som en **SqlDatabaseTransientErrorDetectionStrategy** med ett antal försök på 10 och 5 sekunders väntetid mellan återförsök. Om du använder transaktioner kontrollerar du att omfånget för återförsök går tillbaka till början av transaktionen vid ett tillfälligt fel.

## <a name="limitations"></a>Begränsningar
De tillvägagångssätt som beskrivs i detta dokument medför ett par begränsningar:

* Exempelkoden för det här dokumentet visar inte hur schema hanteras mellan shards.
* Med en begäran antar vi att all dess databasbearbetning finns i en enda shard som identifieras av sharding-nyckeln som tillhandahålls av begäran. Detta antagande håller dock inte alltid, till exempel när det inte är möjligt att göra en sharding-nyckel tillgänglig. För att åtgärda detta innehåller det elastiska databasklientbiblioteket [klassen MultiShardQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Klassen implementerar en anslutningsabstraktion för att fråga över flera shards. Att använda MultiShardQuery i kombination med Dapper ligger utanför det här dokumentets omfattning.

## <a name="conclusion"></a>Slutsats
Program som använder Dapper och DapperExtensions kan enkelt dra nytta av elastiska databasverktyg för Azure SQL Database. Genom de steg som beskrivs i det här dokumentet kan dessa program använda verktygets kapacitet för databeroende routning genom att ändra skapandet och öppnandet av nya [SqlConnection-objekt](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) för att använda [OpenConnectionForKey-anropet](https://msdn.microsoft.com/library/azure/dn807226.aspx) för klientbiblioteket för elastisk databas. Detta begränsar de programändringar som krävs till de platser där nya anslutningar skapas och öppnas. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
