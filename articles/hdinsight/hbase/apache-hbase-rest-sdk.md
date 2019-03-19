---
title: Använd SDK för HBase - Azure HDInsight
description: Använd SDK för HBase för att skapa och ta bort tabeller, och för att läsa och skriva data.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: 27a013bee98447de591d373917c2cbaae7ca08b3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57857665"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Använda .NET SDK för Apache HBase

[Apache HBase](apache-hbase-overview.md) tillhandahåller två primära sätt att arbeta med dina data: [Apache Hive-frågor och Hbase's RESTful API-anrop](apache-hbase-tutorial-get-started-linux.md). Du kan arbeta direkt med en REST-API med hjälp av den `curl` kommando eller ett liknande verktyg.

För C# och .NET-program, den [Microsoft HBase REST-klientbiblioteket för .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) ger ett klientbibliotek utöver HBase REST-API.

## <a name="install-the-sdk"></a>Installera SDK:n

SDK för HBase tillhandahålls som ett NuGet-paket som kan installeras från Visual Studio **NuGet Package Manager Console** med följande kommando:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Skapa en instans av ett nytt HBaseClient-objekt

Om du vill använda SDK: N, skapa en instans av en ny `HBaseClient` objekt som passerar i `ClusterCredentials` består av den `Uri` till ditt kluster och Hadoop-användarnamn och lösenord.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Ersätt KLUSTERNAMN med ditt HDInsight HBase klustrets namn och användarnamn och lösenord med Apache Hadoop-autentiseringsuppgifterna som angetts för klustret skapas. Hadoop-Standardanvändarnamnet är **admin**.

## <a name="create-a-new-table"></a>Skapa en ny tabell

HBase lagrar data i tabeller. En tabell består av en *Rowkey*, den primära nyckeln och en eller flera grupper med kolumner som heter *kolumnserier*. Data i varje tabell distribueras vågrätt med flera Rowkey till *regioner*. Varje region har en start- och slutdatum. En tabell kan ha en eller flera regioner. När data i tabellen växer delar HBase stora regioner i mindre regioner. Regioner lagras i *regionservrar*, där en regionsserver kan lagra flera regioner.

Data lagras fysiskt i *HFiles*. En enda HFile innehåller data för en tabell, en region och en kolumnserie. Rader i HFile lagras sorteras baserat på Rowkey. Varje HFile har en *B +-träd* index för snabb hämtning av rader.

Om du vill skapa en ny tabell, ange en `TableSchema` och kolumner. Följande kod kontrollerar om tabellen 'RestSDKTable' finns redan - om inte tabellen har skapats.

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

Den här nya tabellen har två kolumnserier, t1 och t2. Eftersom kolumnserier lagras separat i olika HFiles kan bra det att ha en separat kolumnfamilj för ofta efterfrågade data. I följande [infoga data](#insert-data) exempelvis kolumner läggs till i kolumnen t1-familjen.

## <a name="delete-a-table"></a>Ta bort en tabell

Ta bort en tabell:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Infoga data

Om du vill infoga data, ange en unik radnyckel som rad-ID. Alla data lagras i en `byte[]` matris. Följande kod definierar och lägger till den `title`, `director`, och `release_date` kolumner till kolumnserie t1 som dessa kolumner är oftast används. Den `description` och `tagline` kolumner läggs till t2 kolumnserie. Du kan partitionera data i kolumnserier efter behov.

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

HBase implementerar [molnet BigTable](https://cloud.google.com/bigtable/), så dataformatet ser ut som följande:

![Användare med rollen kluster](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>Välj data

Om du vill läsa data från en HBase-tabell, skickar du på tabellen namn och en rad för att den `GetCellsAsync` metod för att returnera den `CellSet`.

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

I det här fallet returnerar koden bara den första matchande rad, som det ska bara finnas en rad för en unik nyckel. Det returnerade värdet ändras till `string` formateras från den `byte[]` matris. Du kan också konvertera värdet till andra typer, till exempel ett heltal för filmens lanseringsdatum:

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

HBase använder `scan` att hämta en eller flera rader. Det här exemplet begär flera rader i batchar med 10 och hämtar data vars viktiga värden är mellan 25 och 35. Ta bort skannern att rensa resurser när du hämtar alla rader.

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

* [Kom igång med Apache HBase-exempel i HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Skapa ett program för slutpunkt till slutpunkt med [analysera Twitter-åsikter i realtid med Apache HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
