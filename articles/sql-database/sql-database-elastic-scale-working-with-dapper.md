---
title: Använda elastic database-klientbibliotek med Dapper | Microsoft Docs
description: Använda elastic database-klientbibliotek med Dapper.
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
ms.date: 12/04/2018
ms.openlocfilehash: c6ca7637c8e251fa29781503ffc18227c51bb4da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335305"
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Använda elastic database-klientbibliotek med Dapper
Det här dokumentet är för utvecklare som förlitar sig på Dapper att skapa program, men också vilja ta del av [elastisk databas-tooling](sql-database-elastic-scale-introduction.md) skapar du program att implementera horisontell partitionering för att skala ut datanivån.  Det här dokumentet visar ändringarna i Dapper-baserade program som är nödvändiga för att integrera med verktyg för elastiska databaser. Vår fokus ligger på att skriva fragmenthanterings för elastiska databaser och databeroende routning med Dapper. 

**Exempelkoden**: [Elastic database-verktyg för Azure SQL Database – Dapper integrering](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integrera **Dapper** och **DapperExtensions** med elastic database-klientbiblioteket för Azure SQL Database är enkelt. Dina program kan använda databeroende routning genom att ändra skapandet och öppna i nytt [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekt att använda den [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) anropa från den [klientbibliotek ](https://msdn.microsoft.com/library/azure/dn765902.aspx). Detta begränsar ändringar i ditt program bara där det nya anslutningar skapas och öppnas. 

## <a name="dapper-overview"></a>Dapper översikt
**Dapper** är en objektrelationell mappningskomponent. Det mappas .NET-objekt från ditt program till en relationsdatabas (och vice versa). Den första delen av exempelkoden visar hur du kan integrera elastic database-klientbibliotek med Dapper-baserade program. Den andra delen av exempelkoden visar hur du integrerar när du använder både Dapper och DapperExtensions.  

Funktionen mapper i Dapper innehåller tilläggsmetoder om databasanslutningar som förenklar skickar T-SQL-uttryck för körning eller en fråga till databasen. Exempelvis Dapper gör det enkelt att mappa mellan .NET-objekt och parametrarna för SQL-uttryck för **kör** anrop, eller för att använda resultatet för dina SQL-frågor i .NET-objekt med hjälp av **fråga** anrop från Dapper. 

När du använder DapperExtensions, behöver du inte längre att tillhandahålla SQL-uttryck. Tillägg metoder som **GetList** eller **infoga** över databasanslutningen skapa SQL-instruktioner i bakgrunden.

En annan fördel med Dapper och DapperExtensions är att programmet styr skapandet av anslutningen till databasen. På så sätt kan du interagera med klientbiblioteket för elastiska databaser som mäklare databasanslutningar som baserats på mappningen av shardletar till databaser.

Dapper sammansättningarna finns [Dapper punkt net](https://www.nuget.org/packages/Dapper/). Dapper tillägg, se [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>En snabb inblick i klientbiblioteket för elastiska databaser
Med klientbiblioteket för elastiska databaser kan du definiera partitioner för dina programdata som kallas *shardletar*, mappa den till databaser och identifiera dem genom *horisontell partitionering nycklar*. Du kan ha så många databaser som du behöver och distribuera din shardletar över dessa databaser. Mappningen för horisontell partitionering nyckelvärden till databaser lagras av en skärvkarta som tillhandahålls av API: er i biblioteket. Den här funktionen kallas **fragmentkarthantering**. Fragmentkartan fungerar också som den asynkrona meddelandekön till databaser för begäranden som innehåller en shardingnyckel. Den här funktionen kallas **databeroende routning**.

![Shard-kartor och databeroende Routning][1]

Fragmentkartehanteraren skyddar användare från inkonsekvent vyer i shardlet data som kan uppstå när samtidiga shardlet hanteringsåtgärder sker på databaserna. Om du vill göra det mäkla shard-kartor databasanslutningar för ett program som skapats med biblioteket. När fragment hanteringsåtgärder kan påverka shardlet, på så sätt kan funktionen fragment kartan att automatiskt avsluta en databasanslutning. 

Istället för att använda det traditionella sättet att skapa anslutningar för Dapper, måste du använda den [OpenConnectionForKey metoden](https://msdn.microsoft.com/library/azure/dn824099.aspx). Detta säkerställer att alla verifieringen sker och anslutningar hanteras korrekt när data flyttas mellan shards.

### <a name="requirements-for-dapper-integration"></a>Krav för Dapper integrering
När du arbetar med både elastic database-klientbibliotek och Dapper-API: er som du vill behålla följande egenskaper:

* **Skala ut**: Vi vill lägga till eller ta bort databaser från datanivån för delat program efter behov för kapacitetskraven för programmet. 
* **Konsekvens**: Du behöver utföra databeroende routning eftersom programmet skalas ut med horisontell partitionering. Vi vill använda databeroende routning funktionerna i biblioteket för att göra detta. I synnerhet du vill behålla verifieringen och konsekvens garanterar tillhandahålls av anslutningar som är asynkron via fragmentkartehanteraren för att undvika skadade eller fel frågeresultat. Detta säkerställer att anslutningar till en viss shardlet avvisade eller stoppas om (till exempel) i shardlet för närvarande har flyttats till en annan shard med dela/Sammanslå API: er.
* **Objekt-mappning**: Vi vill behålla praktiskt mappningar som tillhandahålls av Dapper att översätta mellan klasser i programmet och de underliggande strukturerna för databasen. 

Följande avsnitt innehåller råd om kraven för programmen baserat på **Dapper** och **DapperExtensions**.

## <a name="technical-guidance"></a>Teknisk vägledning
### <a name="data-dependent-routing-with-dapper"></a>Databeroende routning med Dapper
Med Dapper är programmet vanligtvis ansvarar för att skapa och öppna anslutningar till den underliggande databasen. En typ T som angetts av programmet, returnerar Dapper frågeresultatet som .NET samlingar av typen T. Dapper utför mappningen från rader för T-SQL-resultatet till objekt av typen T. Dapper mappar på samma sätt kan .NET-objekt till SQL-värden eller parametrar för uttryck för data manipulation language (DML). Dapper erbjuder den här funktionen via tilläggsmetoder på vanliga [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekt från ADO .NET SQL-klientbiblioteken. SQL-anslutning som returneras av Elastic Scale APIs för DDR är också regelbundna [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekt. På så sätt kan vi använda direkt Dapper tillägg över typen som returnerades av klientbiblioteket DDR-API, eftersom det är också en enkel anslutning för SQL-klient.

Dessa observationer gör det enkelt att använda asynkrona av klientbiblioteket för elastiska databaser för Dapper-anslutningar.

Det här kodexemplet (från tillhörande exemplet) visar den metoden där nyckeln för horisontell partitionering tillhandahålls av programmet i biblioteket för att mäkla anslutningen till rätt fragment.   

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

Anropet till den [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) API ersätter standard skapas och öppnas av en SQL-klientanslutning. Den [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) anrop använder argument som krävs för databeroende routning: 

* Fragmentkartan för att komma åt databeroende routning-gränssnitt
* Nyckeln för horisontell partitionering för att identifiera shardlet
* Autentiseringsuppgifterna (användarnamn och lösenord) för att ansluta till fragment

Kartobjekt fragment skapar en anslutning till den shard som innehåller shardlet för den angivna shardingnyckel. Elastic database-klientens API: er tagga också anslutningen till implementera dess konsekvensgarantier. Sedan anropet till [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) returnerar en vanlig SQL Client anslutningsobjektet, efterföljande anrop till den **kör** tilläggsmetod från Dapper följer det Dapper praxis.

Frågor fungerar mycket på samma sätt – du först öppnar anslutningen med [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) från klienten API. Du kan använda de vanliga Dapper tilläggsmetoder för att mappa resultatet av dina SQL-fråga till .NET-objekt:

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

Observera att den **med** blockera alla databasåtgärder i kodblocket till en shard där tenantId1 sparas med DDR anslutning omfång. Frågan returnerar endast bloggar som lagras på den aktuella sharden, men inte de som finns på andra shards. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Databeroende routning med Dapper och DapperExtensions
Dapper levereras med ett ekosystem med ytterligare filnamnstillägg som ger ytterligare bekvämlighet och abstraktion från databasen när du utvecklar databasprogram. DapperExtensions är ett exempel. 

Med hjälp av DapperExtensions i ditt program ändras inte hur databasanslutningar skapas och hanteras. Det är fortfarande programmets ansvar att öppna anslutningar och regelbundna SQL Client-anslutningsobjekt förväntas av metoderna som tillägg. Vi kan lita på den [OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) som beskrivs ovan. Följande kodexempel visas den enda förändringen är att du inte längre behöver skriva T-SQL-uttryck:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Och här är exempelkod för frågan: 

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
Microsoft Patterns & Practices-teamet publicerade den [hantering av Programblocket tillfälliga](https://msdn.microsoft.com/library/hh680934.aspx) kan hjälpa programutvecklare minimera villkor för vanliga tillfälliga fel påträffades vid körning i molnet. Mer information finns i [Perseverance, hemlighet för alla framgångar: Med hjälp av Programblocket för hantering av tillfälliga fel](https://msdn.microsoft.com/library/dn440719.aspx).

Kodexemplet är beroende av tillfälliga fel-biblioteket för att skydda mot tillfälliga fel. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** i koden ovan definieras som en **SqlDatabaseTransientErrorDetectionStrategy** med ett återförsöksvärde på 10 och 5 sekunder väntetid mellan återförsök. Om du använder transaktioner, se till att din omgång för återförsök går tillbaka till början av transaktionen om ett tillfälligt fel.

## <a name="limitations"></a>Begränsningar
De metoder som beskrivs i det här dokumentet medföra några begränsningar:

* Exempelkod för det här dokumentet visar inte hur du hanterar schema över shards.
* Med en begäran kan förutsätter vi att alla dess databasbearbetning ingår i en enda shard som identifieras av shardingnyckel som anges i begäran. Men innehåller här antagandet inte alltid, till exempel när det inte går att göra en shardingnyckel som är tillgängliga. För att lösa det klientbibliotek för elastiska databaser innehåller den [MultiShardQuery klass](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Klassen implementerar en anslutning abstraktion för att fråga över flera fragment. Använda MultiShardQuery i kombination med Dapper ligger utanför omfånget för det här dokumentet.

## <a name="conclusion"></a>Sammanfattning
Program med Dapper och DapperExtensions dra enkelt nytta av verktyg för elastiska databaser för Azure SQL Database. Genom att följa stegen i det här dokumentet, programmen kan använda verktyget kapaciteten för databeroende routning genom att ändra skapandet och öppna i nytt [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekt att använda den [ OpenConnectionForKey](https://msdn.microsoft.com/library/azure/dn807226.aspx) anrop av klientbiblioteket för elastiska databaser. Detta begränsar programändringar som krävs för att de platser där nya anslutningar skapas och öppnas. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
