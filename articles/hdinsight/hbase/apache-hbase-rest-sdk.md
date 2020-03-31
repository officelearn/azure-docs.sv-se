---
title: Använda HBase .NET SDK - Azure HDInsight
description: Använd HBase .NET SDK för att skapa och ta bort tabeller och för att läsa och skriva data.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: eba7d7ad009b2ef0442a916983489489eb5cceb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806668"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Använda .NET SDK för Apache HBase

[Apache HBase](apache-hbase-overview.md) innehåller två primära alternativ för att arbeta med dina data: [Apache Hive-frågor och anrop till HBase's RESTful API](apache-hbase-tutorial-get-started-linux.md). Du kan arbeta direkt med `curl` REST API med kommandot eller ett liknande verktyg.

För C#- och .NET-program tillhandahåller Microsoft HBase REST Client Library för .NET ett klientbibliotek ovanpå HBase REST API.For C# and .NET applications, [Microsoft HBase REST Client Library for .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) provides a client library on top of the HBase REST API.

## <a name="install-the-sdk"></a>Installera SDK:n

HBase .NET SDK tillhandahålls som ett NuGet-paket som kan installeras från Visual Studio **NuGet Package Manager Console** med följande kommando:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Instansiera ett nytt HBaseClient-objekt

Om du vill använda SDK `HBaseClient` instansierar `ClusterCredentials` du ett `Uri` nytt objekt som skickar in ett sammansatt till klustret och Hadoop-användarnamnet och lösenordet.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Ersätt CLUSTERNAME med ditt HDInsight HBase-klusternamn och ANVÄNDARNAMN och LÖSENORD med Apache Hadoop-autentiseringsuppgifterna som anges när klustret skapas. Standardnamnet för Hadoop är **admin**.

## <a name="create-a-new-table"></a>Skapa en ny tabell

HBase lagrar data i tabeller. En tabell består av en *Rowkey*, primärnyckeln och en eller flera grupper av kolumner som kallas *kolumnfamiljer*. Data i varje tabell fördelas vågrätt av ett Rowkey-intervall i *regioner*. Varje region har en start- och slutnyckel. En tabell kan ha en eller flera regioner. När data i tabellen växer delar HBase upp stora regioner i mindre regioner. Regioner lagras i *regionservrar*, där en regionserver kan lagra flera regioner.

Data lagras fysiskt i *HFiles*. En enda HFile innehåller data för en tabell, en region och en kolumnfamilj. Rader i HFile lagras sorterade på Rowkey. Varje HFile har ett *B+ trädindex* för snabb hämtning av raderna.

Om du vill skapa `TableSchema` en ny tabell anger du en och kolumner. Följande kod kontrollerar om tabellen "RestSDKTable" redan finns - om inte, skapas tabellen.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Den här nya tabellen har familjer med två kolumner, t1 och t2. Eftersom kolumnfamiljer lagras separat i olika HFiles är det klokt att ha en separat kolumnfamilj för ofta efterfrågade data. I följande [exempel på Infoga data](#insert-data) läggs kolumner till i t1-kolumnfamiljen.

## <a name="delete-a-table"></a>Ta bort en tabell

Så här tar du bort en tabell:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Infoga data

Om du vill infoga data anger du en unik radnyckel som radidentifierare. Alla data lagras `byte[]` i en matris. Följande kod definierar och `title` `director`lägger `release_date` till kolumnerna , och i t1-kolumnfamiljen, eftersom dessa kolumner är de som används mest. `description` Kolumnerna `tagline` och läggs till i t2-kolumnfamiljen. Du kan dela dina data i kolumnfamiljer efter behov.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase implementerar [Cloud BigTable](https://cloud.google.com/bigtable/), så dataformatet ser ut som följande bild:

![Apache HBase exempeldatautdata](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Välj data

Om du vill läsa data från en HBase-tabell `GetCellsAsync` skickar du `CellSet`tabellnamnet och radtangenten till metoden för att returnera .

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

I det här fallet returnerar koden bara den första matchande raden, eftersom det bara bör finnas en rad för en unik nyckel. Det returnerade värdet `string` ändras `byte[]` till format från matrisen. Du kan också konvertera värdet till andra typer, till exempel ett heltal för filmens utgivningsdatum:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Skanna över rader

HBase `scan` använder för att hämta en eller flera rader. I det här exemplet begärs flera rader i batchar med 10 och data vars nyckelvärden är mellan 25 och 35 hämtas. När du har hämtat alla rader tar du bort skannern för att rensa resurser.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Nästa steg

* [Kom igång med ett Apache HBase-exempel i HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Skapa ett heltäckande program med [Analysera Twitter-sentimentet i realtid med Apache HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
