---
title: Phoenix Query Server REST SDK - Azure HDInsight
description: Installera och använd REST SDK för Phoenix Query Server i Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 84c2bad1004029fe61dcfc19321957a170284587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612265"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>APACHE Phoenix Frågeserver REST SDK

[Apache Phoenix](https://phoenix.apache.org/) är en öppen källkod, massivt parallella relationella databaslager ovanpå [Apache HBase](apache-hbase-overview.md). Phoenix kan du använda SQL-liknande frågor med HBase via SSH-verktyg som [SQLLine](apache-hbase-query-with-phoenix.md). Phoenix tillhandahåller också en HTTP-server som heter Phoenix Query Server (PQS), en tunn klient som stöder två transportmekanismer för klientkommunikation: JSON och protokollbuffertar. Protokollbuffertar är standardmekanismen och erbjuder effektivare kommunikation än JSON.

I den här artikeln beskrivs hur du använder PQS REST SDK för att skapa tabeller, uppåtriktade rader individuellt och i grupp, och välja data med SQL-uttryck. I exemplen används [Microsoft .NET-drivrutinen för Apache Phoenix Query Server](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Den här SDK bygger på [Apache Calcite's Avatica](https://calcite.apache.org/avatica/) API:er, som uteslutande använder protokollbuffertar för serialiseringsformatet.

Mer information finns i [Apache Calcite Avatica Protocol Buffers Reference](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Installera SDK:n

Microsoft .NET-drivrutin för Apache Phoenix Query Server tillhandahålls som ett NuGet-paket som kan installeras från Visual Studio **NuGet Package Manager Console** med följande kommando:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Instantiate nya PhoenixClient objekt

Om du vill börja använda biblioteket `PhoenixClient` instansierar `ClusterCredentials` du `Uri` ett nytt objekt och skickar in det till klustret och klustrets Apache Hadoop-användarnamn och lösenord.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Ersätt CLUSTERNAME med ditt HDInsight HBase-klusternamn och ANVÄNDARNAMN och LÖSENORD med de Hadoop-autentiseringsuppgifter som angetts när klustret skapas. Standardnamnet för Hadoop är **admin**.

## <a name="generate-unique-connection-identifier"></a>Generera unik anslutningsidentifierare

Om du vill skicka en eller flera begäranden till PQS måste du inkludera en unik anslutningsidentifierare för att associera begäran/begäranden med anslutningen.

```csharp
string connId = Guid.NewGuid().ToString();
```

Varje exempel anropar `OpenConnectionRequestAsync` först metoden och skickar in den unika anslutningsidentifieraren. Definiera `ConnectionProperties` sedan `RequestOptions`och skicka dessa objekt och den `ConnectionSyncRequestAsync` genererade anslutningsidentifieraren till metoden. PQS `ConnectionSyncRequest` objekt hjälper till att säkerställa att både klienten och servern har en konsekvent bild av databasegenskaperna.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest och dess ConnectionProperties

Om `ConnectionSyncRequestAsync`du vill `ConnectionProperties` ringa skickar du in ett objekt.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Här är några egenskaper av intresse:

| Egenskap | Beskrivning |
| -- | -- |
| AutoCommit | En boolesk som `autoCommit` anger om är aktiverad för Phoenix-transaktioner. |
| ReadOnly | En boolesk som anger om anslutningen är skrivskyddad. |
| Transaktionisolation | Ett heltal som anger transaktionssoleringen enligt JDBC-specifikationen - se följande tabell.|
| Katalog | Namnet på den katalog som ska användas när anslutningsegenskaper hämtas. |
| Schema | Namnet på det schema som ska användas när anslutningsegenskaper hämtas. |
| IsDirty (030) | En boolesk som anger om egenskaperna har ändrats. |

Här är `TransactionIsolation` värdena:

| Isoleringsvärde | Beskrivning |
| -- | -- |
| 0 | Transaktioner stöds inte. |
| 1 | Smutsiga läsningar, icke-repeterbara läsningar och fantomläsningar kan förekomma. |
| 2 | Smutsiga läsningar förhindras, men icke-repeterbara läsningar och fantomläsningar kan förekomma. |
| 4 | Smutsiga läsningar och icke-repeterbara läsningar förhindras, men fantomläsningar kan förekomma. |
| 8 | Smutsiga läsningar, icke-repeterbara läsningar och fantomläsningar förhindras alla. |

## <a name="create-a-new-table"></a>Skapa en ny tabell

HBase lagrar, precis som alla andra RDBMS, data i tabeller. Phoenix använder vanliga SQL-frågor för att skapa nya tabeller, samtidigt som primärnyckeln och kolumntyperna definieras.

I det här exemplet och alla `PhoenixClient` senare exempel använder du det instansierade objektet enligt definitionen i [Instansiera ett nytt PhoenixClient-objekt](#instantiate-new-phoenixclient-object).

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

I föregående exempel skapas en `Customers` ny `IF NOT EXISTS` tabell med namnet med hjälp av alternativet. Anropet `CreateStatementRequestAsync` skapar ett nytt uttryck i Avitica -servern (PQS). Blocket `finally` stänger den `CreateStatementResponse` returnerade `OpenConnectionResponse` och objekten.

## <a name="insert-data-individually"></a>Infoga data individuellt

I det här exemplet visas en enskild `List<string>` datainfogning som refererar till en samling amerikanska delstats- och områdesförkortningar:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

Tabellens `StateProvince` kolumnvärde används i en senare urvalsåtgärd.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Strukturen för att köra en infoga-sats påminner om att skapa en ny tabell. I slutet av `try` blocket genomförs transaktionen uttryckligen. I det här exemplet upprepas en infoga-transaktion 300 gånger. I följande exempel visas en effektivare batchinfogningsprocess.

## <a name="batch-insert-data"></a>Batch infoga data

Följande kod är nästan identisk med koden för att infoga data individuellt. I det `UpdateBatch` här exemplet används `ExecuteBatchRequestAsync`objektet i ett `ExecuteRequestAsync` anrop till , i stället för att upprepade gånger anropa med ett förberett utdrag.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

I en testmiljö tog det nästan 2 minuter att infoga 300 nya poster individuellt. Däremot krävs bara 6 sekunder för att infoga 300 poster som en batch.

## <a name="select-data"></a>Välj data

I det här exemplet visas hur du återanvänder en anslutning för att köra flera frågor:

1. Markera alla poster och hämta sedan återstående poster efter att standardmaxaligheten 100 returneras.
2. Använd en total radräkningssats för att hämta det enda skalärresultatet.
3. Kör en select-sats som returnerar det totala antalet kunder per tillstånd eller område.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Resultatet av `select` uttalandena bör vara följande resultat:

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Nästa steg

* [Apache Phoenix i HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Använda Apache HBase REST SDK](apache-hbase-rest-sdk.md)
