---
title: Phoenix frågeserver REST SDK - Azure HDInsight | Microsoft Docs
description: ''
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: ef89bcea3eab92c3137a6f532398764462ae204c
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164592"
---
# <a name="phoenix-query-server-rest-sdk"></a>Phoenix frågeserver REST-SDK

[Apache Phoenix](http://phoenix.apache.org/) är en öppen källkod, massivt parallell relationsdatabaslager över [HBase](apache-hbase-overview.md). Phoenix kan du använda SQL-liknande frågor med HBase via SSH verktyg som [SQLLine](apache-hbase-phoenix-squirrel-linux.md). Phoenix innehåller också en HTTP-server som kallas Phoenix frågan Server (PQS), en tunn klient som stöder två transportmekanismer för klientkommunikation: JSON och protokollet buffertar. Protokollet buffertar är standardmekanism och ger mer effektiv kommunikation än JSON.

Den här artikeln beskriver hur du använder PQS REST SDK för att skapa tabeller, upsert rader och separat gruppvis och välj data med hjälp av SQL-instruktioner. Exemplen använder den [Microsoft .NET-drivrutin för Apache Phoenix fråge-](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Detta SDK bygger på [Apache Calcite Avatica](https://calcite.apache.org/avatica/) API: er, som endast använder protokollet buffertar för serialiseringsformat.

Mer information finns i [buffertar för Apache Calcite Avatica Protokollreferens](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Installera SDK:n

Microsoft .NET-drivrutin för Apache Phoenix fråge-tillhandahålls som ett NuGet-paket som kan installeras från Visual Studio **NuGet Package Manager-konsolen** med följande kommando:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Initiera ny PhoenixClient objekt

Om du vill börja använda biblioteket måste initiera en ny `PhoenixClient` objekt som passerar i `ClusterCredentials` som innehåller den `Uri` till klustret och klustrets Hadoop-användarnamn och lösenord.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Ersätt KLUSTERNAMN med ditt HDInsight HBase klustrets namn och användarnamn och lösenord med Hadoop-autentiseringsuppgifter som anges i klustret har skapats. Hadoop-Standardanvändarnamnet är **admin**.

## <a name="generate-unique-connection-identifier"></a>Generera en unik anslutningsidentifierare

Om du vill skicka ett eller flera begäranden till PQS, måste du inkludera en unik anslutningsidentifierare om du vill associera behandlas med anslutningen.

```csharp
string connId = Guid.NewGuid().ToString();
```

Varje exempel först gör ett anrop till den `OpenConnectionRequestAsync` metoden och skickar i unika anslutningsidentifierare. Härnäst definierar `ConnectionProperties` och `RequestOptions`, passerar de objekt och den genererade anslutning till den `ConnectionSyncRequestAsync` metoden. PQS'S `ConnectionSyncRequest` objektet kan du kontrollera att både klienten och servern har en konsekvent överblick över databasens egenskaper.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest och dess ConnectionProperties

Att anropa `ConnectionSyncRequestAsync`, skicka in en `ConnectionProperties` objekt.

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

Här följer några egenskaper för intresse:

| Egenskap  | Beskrivning |
| -- | -- |
| AutoCommit | Ett booleskt som anger om `autoCommit` har aktiverats för Phoenix transaktioner. |
| Skrivskyddad | Ett booleskt värde som anger om anslutningen är skrivskyddad. |
| TransactionIsolation | Ett heltal som anger nivån av transaktionsisoleringen per JDBC-specifikationen - finns i följande tabell.|
| Katalog | Namnet på katalogen som ska användas vid hämtning av egenskaper för anslutning. |
| Schema | Namnet på scheman du ska använda vid hämtning av egenskaper för anslutning. |
| IsDirty | Ett booleskt värde som anger om egenskaperna har ändrats. |

Här är den `TransactionIsolation` värden:

| Värdet för isolering | Beskrivning |
| -- | -- |
| 0 | Transaktioner stöds inte. |
| 1 | Felaktiga läsningar och icke repeterbara läsningar fiktiva läsningar kan uppstå. |
| 2 | Felaktiga läsningar förhindras, men icke repeterbara läsningar och fiktiva läsningar kan uppstå. |
| 4 | Förhindras felaktiga läsningar och icke repeterbara läsningar men fiktiva läsningar kan uppstå. |
| 8 | Alla förhindras felaktiga läsningar och icke repeterbara läsningar fiktiva läsningar. |

## <a name="create-a-new-table"></a>Skapa en ny tabell

HBase som andra RDBMS lagrar data i tabeller. Phoenix använder standard SQL-frågor för att skapa nya tabeller när du definierar vilka primära nyckel och kolumn.

Det här exemplet och alla efterföljande exemplen använder den instansierad `PhoenixClient` objekt som har definierats i [instansiera ett nytt objekt i PhoenixClient](#instantiate-new-phoenixclient-object).

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

I föregående exempel skapas en ny tabell med namnet `Customers` med hjälp av den `IF NOT EXISTS` alternativet. Den `CreateStatementRequestAsync` anrop skapas ett nytt uttryck i Avitica (PQS)-server. Den `finally` block stängs den returnerade `CreateStatementResponse` och `OpenConnectionResponse` objekt.

## <a name="insert-data-individually"></a>Infoga data individuellt

Det här exemplet illustrerar en enskilda data insert-, refererar till en `List<string>` samling American tillstånd och territorium förkortningar:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

Tabellens `StateProvince` värde i kolumnen som ska användas i efterföljande select-åtgärden.

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

Strukturen för att köra en insert-instruktion liknar skapar en ny tabell. Observera att i slutet av den `try` block transaktionen strävar explicit. Det här exemplet upprepas en insert-transaktion 300 gånger. I följande exempel visas en effektivare insert batchprocess.

## <a name="batch-insert-data"></a>Batch infoga data

Följande kod är nästan identisk med koden för att infoga data individuellt. Det här exemplet används den `UpdateBatch` objekt vid ett anrop till `ExecuteBatchRequestAsync`, i stället för att anropa upprepade gånger `ExecuteRequestAsync` med en förberedd instruktion.

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

I en testmiljö där tog 300 nya poster som infogas individuellt nästan 2 minuter. Däremot krävs infogar 300 poster som en batch endast 6 sekunder.

## <a name="select-data"></a>Välj data

Det här exemplet visar hur du kan återanvända en anslutning för att köra flera frågor:

1. Välj alla poster och hämta sedan återstående poster efter maximalt värde 100 returneras.
2. Använda en summarad antalet select-instruktion för att hämta skalära resultatet.
3. Köra en select-instruktion som returnerar det totala antalet kunder per stat eller territorium.

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

Utdata från den `select` instruktioner bör vara följande:

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

* [Phoenix i HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Med hjälp av HBase REST-SDK](apache-hbase-rest-sdk.md)
