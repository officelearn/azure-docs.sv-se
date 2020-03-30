---
title: Databeroende routning
description: Så här använder du klassen ShardMapManager i .NET-appar för databeroende routning, en funktion i fragmenterade databaser i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: fbdf8e316368be02ebd0c4bfd320917c20d80777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069463"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Använda databeroende routning för att dirigera en fråga till lämplig databas

**Databeroende routning** är möjligheten att använda data i en fråga för att dirigera begäran till en lämplig databas. Databeroende routning är ett grundläggande mönster när du arbetar med fragmenterade databaser. Begäran-kontexten kan också användas för att dirigera begäran, särskilt om sharding-nyckeln inte är en del av frågan. Varje specifik fråga eller transaktion i ett program som använder databeroende routning är begränsad till åtkomst till en databas per begäran. För Azure SQL Database Elastic-verktygen utförs den här routningen med klassen **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)

Programmet behöver inte spåra olika anslutningssträngar eller DB-platser som är associerade med olika datasegment i den fragmenterade miljön. I stället öppnar [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md) anslutningar till rätt databaser när det behövs, baserat på data i fragmentkartan och värdet på sharding-nyckeln som är målet för programmets begäran. Nyckeln är vanligtvis *customer_id*, *tenant_id*, *date_key*eller någon annan specifik identifierare som är en grundläggande parameter för databasbegäran.

Mer information finns i [Skala ut SQL Server med databeroende routning](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Ladda ner klientbiblioteket

Så här laddar du ned:

* Java-versionen av biblioteket, se [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET-versionen av biblioteket, se [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Använda en ShardMapManager i ett databeroende routningsprogram

Program bör instansiera **ShardMapManager** under initieringen, med hjälp av **fabriksanropet GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). I det här exemplet initieras både en **ShardMapManager** och en specifik **ShardMap** som den innehåller. I det här exemplet visas metoderna GetSqlShardMapManager och GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET).](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Använd lägsta behörighetsautentiseringsuppgifter som är möjliga för att hämta fragmentkartan

Om ett program inte manipulerar själva fragmentmappningen bör autentiseringsuppgifterna som används i fabriksmetoden ha skrivskyddade behörigheter i databasen **Global Shard Map.** Dessa autentiseringsuppgifter skiljer sig vanligtvis från autentiseringsuppgifter som används för att öppna anslutningar till fragmentkarthanteraren. Se även [Autentiseringsuppgifter som används för att komma åt klientbiblioteket för elastisk databas](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Anropa metoden OpenConnectionForKey

**ShardMap.OpenConnectionForKey-metoden** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) returnerar en anslutning som är klar för utfärdande av kommandon till lämplig databas baserat på värdet för nyckelparametern. **key** Shard-information cachelagras i programmet av **ShardMapManager**, så dessa begäranden innebär vanligtvis inte en databassökning mot den **globala fragmentkartdatabasen.**

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* **Nyckelparametern** används som en uppslagsnyckel i fragmentmappningen för att fastställa lämplig databas för begäran.
* **ConnectionString** används för att endast skicka användarautentiseringsuppgifterna för önskad anslutning. Inget databasnamn eller servernamn ingår i den här *anslutningenSträngning* eftersom metoden bestämmer databasen och servern med hjälp av **ShardMap**.
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions) [, .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) bör ställas in på **ConnectionOptions.Validate** om en miljö där fragmentkartor kan ändras och rader kan flyttas till andra databaser som ett resultat av delnings- eller sammanslagningsåtgärder. Den här valideringen innebär en kort fråga till den lokala fragmentmappningen i måldatabasen (inte till den globala fragmentkartan) innan anslutningen levereras till programmet.

Om valideringen mot den lokala fragmentmappningen misslyckas (vilket indikerar att cacheminnet är felaktigt) frågar Shard Map Manager den globala fragmentmappningen för att hämta det nya korrekta värdet för sökningen, uppdaterar cacheminnet och hämtar och returnerar lämplig databasanslutning .

Använd **ConnectionOptions.None** endast när fragmentmappningsändringar inte förväntas när ett program är online. I så fall kan de cachelagrade värdena antas alltid vara korrekta, och det extra valideringsanropet för rundturer till måldatabasen kan hoppas över på ett säkert sätt. Det minskar databastrafiken. **ConnectionOptions** kan också ställas in via ett värde i en konfigurationsfil för att ange om sharding-ändringar förväntas eller inte under en tidsperiod.  

I det här exemplet används värdet för ett heltalsnyckel **CustomerID**med hjälp av ett **ShardMap-objekt** med namnet **customerShardMap**.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

Metoden **OpenConnectionForKey** returnerar en ny redan öppen anslutning till rätt databas. Anslutningar som används på detta sätt drar fortfarande full nytta av anslutningspoolning.

**Metoden OpenConnectionForKeyAsync** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync) [, .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) är också tillgänglig om programmet använder asynkron programmering.

## <a name="integrating-with-transient-fault-handling"></a>Integrering med övergående felhantering

Bästa praxis för att utveckla dataåtkomstprogram i molnet är att se till att tillfälliga fel fångas upp av appen och att åtgärderna görs om flera gånger innan ett fel utlöses. Tillfällig felhantering för molnprogram diskuteras vid Transient Fault Handling[(Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Tillfällig felhantering kan samexistera naturligt med det databeroende routningsmönstret. Det viktigaste kravet är att försöka igen hela dataåtkomstbegäran, inklusive **det medföljande** blocket som erhöll den databeroende routningsanslutningen. Föregående exempel kan skrivas om på följande sätt.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exempel - databeroende routning med tillfällig felhantering

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

Paket som krävs för att implementera tillfällig felhantering hämtas automatiskt när du skapar det elastiska databasexempelprogrammet.

## <a name="transactional-consistency"></a>Transaktionell konsekvens

Transaktionsegenskaper garanteras för alla åtgärder som är lokala till en fragment. Till exempel körs transaktioner som skickas via databeroende routning inom ramen för målsvanten för anslutningen. För närvarande finns det inga funktioner för att värva flera anslutningar till en transaktion, och därför finns det inga transaktionsgarantier för åtgärder som utförs över shards.

## <a name="next-steps"></a>Nästa steg

Om du vill ta bort en shard eller fästa om en shard läser [du Använda klassen RecoveryManager för att åtgärda problem med fragmentmappning](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
