---
title: Klientbibliotek för elastisk databas med Dapper | Microsoft Docs
description: Med Dapper klientbibliotek för elastisk databas.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 6619f2dfe5f58cd23dbd0ffe6e2b545b803f3acc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647936"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Med Dapper klientbibliotek för elastisk databas
Det här dokumentet är för utvecklare som förlitar sig på Dapper att bygga program, men också att omfatta [elastisk databas-tooling](sql-database-elastic-scale-introduction.md) att skapa program att implementera horisontell partitionering att skala ut datanivå sina.  Det här dokumentet visar ändringarna i Dapper-baserade program som är nödvändiga för att integrera med elastiska Databasverktyg. Vår fokus ligger på fastställdes elastisk databas Fragmentera hanterings- och data-beroende routning med Dapper. 

**Exempelkoden**: [elastisk Databasverktyg för Azure SQL Database - Dapper integrering](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrera **Dapper** och **DapperExtensions** med elastisk databas klientbiblioteket för Azure SQL Database är enkelt. Dina program kan använda data beroende routning genom att ändra skapandet och öppna i nytt [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekt som ska använda den [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) anropa från den [klientbibliotek ](http://msdn.microsoft.com/library/azure/dn765902.aspx). Detta begränsar ändringar i ditt program till endast där nya anslutningar skapas och öppnas. 

## <a name="dapper-overview"></a>Dapper översikt
**Dapper** är en Objektrelationer mappning. .NET-objekt från ditt program i en relationsdatabas (och vice versa) mappas. Den första delen av exempelkoden illustrerar hur du kan integrera klientbibliotek för elastisk databas med Dapper-baserade program. Den andra delen av exempelkoden illustrerar hur du integrerar när du använder både Dapper och DapperExtensions.  

Funktionen mapper i Dapper innehåller tilläggsmetoder om databasanslutningar som förenklar skickar T-SQL-uttryck för körning eller en fråga till databasen. Till exempel Dapper gör det enkelt att mappa mellan .NET-objekt och parametrar för SQL-uttryck för **kör** anrop, eller att använda resultatet av dina SQL-frågor till .NET-objekt med hjälp av **frågan** anrop från Dapper. 

När du använder DapperExtensions, behöver du inte längre ange SQL-instruktioner. Tillägg metoder som **GetList** eller **infoga** över databasanslutningen skapa SQL-instruktioner i bakgrunden.

En annan fördel med Dapper och DapperExtensions är att programmet styr skapandet av anslutningen till databasen. På så sätt kan interagera med klientbibliotek för elastisk databas som mäklare databasanslutningar som baserats på mappningen av shardlets till databaser.

Dapper sammansättningarna finns [Dapper punkt net](http://www.nuget.org/packages/Dapper/). Dapper tillägg finns [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>En titt på klientbibliotek för elastisk databas
Du kan definiera partitioner för dina programdata kallas med klientbibliotek för elastisk databas *shardlets*, mappa dem till databaser och identifiera dem genom *nycklar för horisontell partitionering*. Du kan ha valfritt antal databaser du behöver och distribuera din shardlets mellan dessa databaser. Mappningen för horisontell partitionering nyckelvärden till databaserna lagras av en Fragmentera karta som tillhandahålls av API: er i biblioteket. Den här funktionen kallas **Fragmentera kartan management**. Fragmentera kartan fungerar också som Service broker för databasanslutningar för begäranden som innehåller en nyckel för horisontell partitionering. Den här funktionen kallas **data beroende routning**.

![Fragmentera mappar och data beroende Routning][1]

Fragmentera kartan manager skyddar användare från inkonsekvent vyer i shardlet data som kan uppstå när samtidiga shardlet hanteringsåtgärder sker på databaserna. Om du vill göra det, broker Fragmentera maps databasanslutningar för ett program som skapats med biblioteket. Fragmentera hanteringsåtgärder kan påverka shardlet, kan funktionen Fragmentera kartan att avsluta en databasanslutning automatiskt. 

Istället för att använda det traditionella sättet att skapa anslutningar för Dapper, måste du använda den [OpenConnectionForKey metoden](http://msdn.microsoft.com/library/azure/dn824099.aspx). Detta säkerställer att alla valideringsregler sker och anslutningar hanteras korrekt när data flyttas mellan shards.

### <a name="requirements-for-dapper-integration"></a>Krav för Dapper integrering
När du arbetar med både klientbibliotek för elastisk databas och Dapper-API: er som du vill behålla följande egenskaper:

* **Skala ut**: vi vill lägga till eller ta bort databaser från datanivå av delat programmet efter behov för kapacitetskraven för programmet. 
* **Konsekvenskontroll**: eftersom programmet utskalad med horisontell partitionering, måste du utföra data beroende routning. Vi vill göra det med hjälp av data-beroende routning funktionerna i biblioteket. I synnerhet du vill behålla verifieringen och konsekvens garanterar tillhandahålls av anslutningar som asynkrona via hanteraren Fragmentera kartan för att undvika skador eller fel frågeresultat. Detta säkerställer att anslutningar till en viss shardlet avvisade eller stoppas om (till exempel) i shardlet för närvarande har flyttats till en annan Fragmentera med delade/Merge API: er.
* **Objektmappningen**: vi vill behålla bekvämlighet mappning som tillhandahålls av Dapper att översätta mellan klasser i programmet och de underliggande databasen strukturerna. 

Följande avsnitt innehåller information om kraven för programmen baserat på **Dapper** och **DapperExtensions**.

## <a name="technical-guidance"></a>Teknisk information
### <a name="data-dependent-routing-with-dapper"></a>Data dependent routning med Dapper
Programmet är vanligtvis ansvarar för att skapa och öppna anslutningar till den underliggande databasen med Dapper. En typ T som angetts av programmet, returnerar Dapper frågeresultat som .NET samlingar av typen T. Dapper utför mappningen från rader för T-SQL-resultatet till objekt av typen T. På liknande sätt mappar Dapper .NET-objekt till SQL-värden eller parametrar för uttryck data manipulation language (DML). Dapper erbjuder den här funktionen via tilläggsmetoder på vanliga [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekt från ADO .NET SQL-klientbibliotek. SQL-anslutning som returneras av Elastic Scale API: er för DDR är också regelbundna [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekt. På så sätt kan vi använda direkt Dapper tillägg över typen som returnerades av klientbiblioteket DDR-API, eftersom det är en enkel anslutning för SQL-klienten.

Dessa observationer gör det lätt att använda asynkrona av klientbiblioteket för elastisk databas för Dapper-anslutningar.

Den här kodexempel (från det medföljande provet) visas en metod där nyckeln för horisontell partitionering tillhandahålls av programmet till biblioteket till broker anslutningen till rätt Fragmentera.   

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

Anropet till den [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) API ersätter standard skapas eller öppnas av en SQL-klientanslutning. Den [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) anrop tar argument som krävs för data-beroende routning: 

* Fragmentera kartan för att få åtkomst till data-beroende routning-gränssnitt
* Nyckeln delning för att identifiera shardlet
* Autentiseringsuppgifter (användarnamn och lösenord) för att ansluta till Fragmentera

Objektet Fragmentera kartan skapar en anslutning till Fragmentera som innehåller shardlet för den angivna horisontell partitionering nyckeln. Elastisk databas-klientens API: er tagga också anslutningen att implementera dess konsekvens garanterar. Sedan anropet till [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) returnerar en vanlig SQL objekt för klientanslutning, efterföljande anrop till den **Execute** metod från Dapper följer standard Dapper övningen.

Frågor fungerar mycket på samma sätt – du först öppna en anslutning med hjälp av [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) från klienten API. Du kan använda reguljära Dapper tilläggsmetoder för att mappa resultaten av SQL-frågan till .NET-objekt:

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

Observera att den **med** blockera alla databasåtgärder i block till en Fragmentera där tenantId1 sparas med Sökomfång för DDR-anslutning. Frågan returnerar endast bloggar som lagras på den aktuella Fragmentera, men inte de som finns på andra delar. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Data dependent routning med Dapper och DapperExtensions
Dapper levereras med en ekosystem med ytterligare filnamnstillägg som kan ge ytterligare underlätta för dig och abstraction från databasen när du utvecklar databasprogram. DapperExtensions är ett exempel. 

Med hjälp av DapperExtensions i ditt program ändrar inte hur databasanslutningar skapas och hanteras. Det är fortfarande programmets ansvar för att öppna anslutningar och regelbundna SQL Client anslutningsobjekt förväntas av tillägget. Vi kan utgå ifrån den [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) som beskrivs ovan. Som i följande kodexempel visar är den enda förändringen att du inte längre behöver skriva T-SQL-uttryck:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Och här kodexemplet för frågan: 

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

### <a name="handling-transient-faults"></a>Hantering av tillfälliga problem
Microsoft Patterns & Practices teamet publicerade den [tillfälliga fel hanterar programmet Block](http://msdn.microsoft.com/library/hh680934.aspx) att programutvecklare minimera villkor för vanliga tillfälligt fel uppstod när den körs i molnet. Mer information finns i [Perseverance, hemligheten för alla framgångar: med hjälp av tillfälliga fel hanterar programmet blocket](http://msdn.microsoft.com/library/dn440719.aspx).

Kodexemplet förlitar sig på tillfälliga fel-biblioteket för att skydda mot tillfälliga problem. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** i koden ovan har definierats som en **SqlDatabaseTransientErrorDetectionStrategy** med ett återförsöksvärde på 10 och 5 sekunder väntetid mellan återförsök. Om du använder transaktioner, kontrollerar du att försök omfattningen går tillbaka till början av transaktionen om ett tillfälligt fel.

## <a name="limitations"></a>Begränsningar
De metoder som beskrivs i det här dokumentet medför några begränsningar:

* Exempelkod för det här dokumentet visar inte hur du hanterar schemat över shards.
* Ges en begäran om antar vi att dess databasbearbetning ingår i en enda Fragmentera som identifieras av horisontell partitionering-nyckel som tillhandahålls av begäran. Men innehåller detta antagande inte alltid, till exempel när det inte är möjligt att tillhandahålla en nyckel för horisontell partitionering. För att lösa det klientbibliotek för elastisk databas innehåller den [MultiShardQuery klassen](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Klassen implementerar en anslutning abstraktion för frågor över flera delar. Använda MultiShardQuery i kombination med Dapper är utanför omfattningen för det här dokumentet.

## <a name="conclusion"></a>Sammanfattning
Program med hjälp av Dapper och DapperExtensions kan enkelt utnyttja elastisk Databasverktyg för Azure SQL Database. Via de steg som beskrivs i det här dokumentet programmen kan använda verktyget kapaciteten för data-beroende routning genom att ändra skapandet och öppna i nytt [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekt som ska använda den [ OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) anrop av klientbiblioteket för elastisk databas. Detta begränsar programändringar som krävs för att de platser där nya anslutningar skapas och öppnas. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
