---
title: Databeroende routning med Azure SQL Database | Microsoft Docs
description: Hur du använder klassen ShardMapManager i .NET-appar för databeroende routning, en funktion i shardade databaser i Azure SQL Database
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
ms.date: 01/25/2019
ms.openlocfilehash: fe9098592fcfde2d5e23b78a3e33f2b4ebb9e2dc
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468656"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Använda databeroende routning för att dirigera en fråga till lämplig databas

**Databeroende routning** är möjligheten att använda data i en fråga för att dirigera begäran till en lämplig databas. Data beroende routning är ett grundläggande mönster när du arbetar med shardade databaser. Kontext för begäran kan också användas för att dirigera begäran, särskilt om nyckeln för horisontell partitionering inte är en del av frågan. Varje specifik fråga eller en transaktion i ett program med databeroende routning är begränsad till åtkomst till en databas per begäran. För Azure SQL Database elastiska verktyg, den här routning åstadkoms med den **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) klass.

Programmet behöver inte spåra olika anslutningssträngar eller DB-platser som är associerade med olika datasegment i shardade miljön. I stället den [Karthanteraren](sql-database-elastic-scale-shard-map-management.md) öppnas anslutningar till rätt databas vid behov, baserat på data i fragmentkartan och värdet för nyckeln för horisontell partitionering som är mål för programmets begäran. Nyckeln är vanligtvis den *customer_id*, *tenant_id*, *date_key*, eller vissa specifika identifierare som är en grundläggande parameter för databasbegäran.

Mer information finns i [skala ut SQL Server med databeroende routning](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Hämta klientbiblioteket

Så här hämtar:

* Java-versionen av biblioteket, se [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET-versionen av biblioteket, se [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Använd en ShardMapManager i ett databeroende routning program

Program ska skapa en instans av den **ShardMapManager** med hjälp av factory-anrop under initieringen **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). I det här exemplet både en **ShardMapManager** och en specifik **ShardMap** som den innehåller initieras. Det här exemplet visar GetSqlShardMapManager och GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))) metoder.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Använd lägsta behörighet autentiseringsuppgifter som är möjligt för att hämta fragmentkartan

Om ett program inte manipulera fragmentkartan själva, de autentiseringsuppgifter som används i standardmetoden bör ha läsbehörighet den **globala Fragmentkartan** databas. De här autentiseringsuppgifterna är vanligtvis skiljer sig från autentiseringsuppgifterna som används för att öppna anslutningar till fragmentkartehanteraren. Se även [autentiseringsuppgifter används för åtkomst till klientbiblioteket för elastiska databaser](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Anropa metoden OpenConnectionForKey

Den **ShardMap.OpenConnectionForKey metoden** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) returnerar en anslutning som är redo för att utfärda kommandon till lämplig databas baserat på värdet för den **nyckel** parametern. Dela informationen cachelagras i programmet genom att den **ShardMapManager**, så att dessa begäranden inte normalt omfattar en databassökning mot den **globala Fragmentkartan** databas.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* Den **nyckel** parametern används som en lookup-nyckel i fragmentkartan för att fastställa lämplig databas för begäran.
* Den **connectionString** används för att skicka endast användaruppgifter för önskad anslutning. Inget databasnamn eller servernamn som ingår i den här *connectionString* eftersom metoden anger databasen och servern med den **ShardMap**.
* Den **connectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) ska vara inställd på **ConnectionOptions.Validate** om en miljö där shardkartor maj ändra och rader kan flyttas till andra databaser på grund av delad tunnel eller merge-åtgärder. Den här verifieringen inbegriper en kortfattad fråga till den lokala fragmentkartan på mål-databasen (inte till den globala fragmentkartan) innan anslutningen kan levereras till programmet.

Om verifieras mot lokala fragmentkartan (som anger att cachen är felaktig) misslyckas frågar Fragmentkartehanteraren globala fragmentkartan för att hämta nya rätt värde för sökningen, uppdatera cachen och hämta och returnera rätt databasanslutningen .

Använd **ConnectionOptions.None** endast när fragment mappningsändringar inte förväntas när ett program är online. I så fall kan cachelagrade värdena kan antas att alltid vara korrekta och extra fram och åter verifiering anropet till måldatabasen kan hoppas över på ett säkert sätt. Som minskar databastrafik. Den **connectionOptions** kan också anges via ett värde i en konfigurationsfil för att indikera om ändringar för horisontell partitionering förväntas eller inte under en viss tidsperiod.  

Det här exemplet används värdet för ett integer-nyckeln **CustomerID**med hjälp av en **ShardMap** objekt med namnet **customerShardMap**.  

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

Den **OpenConnectionForKey** metoden returnerar en ny redan öppen anslutning till rätt databas. Anslutningar som används i det här sättet kan fortfarande dra full nytta av anslutningspooler.

Den **OpenConnectionForKeyAsync metoden** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) är också tillgängligt om din tillämpning utför Använd asynkron programmering.

## <a name="integrating-with-transient-fault-handling"></a>Integrera med hantering av tillfälliga fel

Bästa praxis i att utveckla program för åtkomst av data i molnet är att se till att tillfälliga fel fångas av appen och att åtgärderna försöks igen flera gånger innan du utlösa ett fel. Hantering av för molnprogram tillfälliga fel beskrivs vid hantering av tillfälliga fel ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Hantering av tillfälliga fel kan samexistera naturligt med mönstret databeroende routning. Det viktigaste kravet är att försöka igen i hela begäran, inklusive den **med** block som fick databeroende routning anslutningen. I föregående exempel kan skrivas på följande sätt.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exempel – databeroende routning med hantering av tillfälliga fel

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

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt;
{
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

Paket som är nödvändiga för att implementera hantering av tillfälliga fel laddas ned automatiskt när du skapar elastiska databaser exempelprogrammet.

## <a name="transactional-consistency"></a>Transaktionell konsekvens

Egenskaper för transaktionell garanterat för alla åtgärder lokala till en shard. Till exempel köra transaktioner som har skickats via databeroende routning inom omfånget för mål-fragment för anslutningen. För tillfället finns inga funktioner för att ta med flera anslutningar i en transaktion och det finns därför inga transaktionella garantier för åtgärder som utförs över shards.

## <a name="next-steps"></a>Nästa steg

Koppla från en shard eller återansluta ett fragment i avsnittet [korrigera shard-kartproblem med RecoveryManager-klassen](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]