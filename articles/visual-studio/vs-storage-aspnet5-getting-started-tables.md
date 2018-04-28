---
title: Hur du kommer igång med tabellagring och Visual Studio anslutna tjänster (ASP.NET Core) | Microsoft Docs
description: Hur du kommer igång med Azure Table storage i ASP.NET Core-projekt i Visual Studio efter anslutning till ett lagringskonto med hjälp av Visual Studio anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: e53e8ed27cfc048f24bda4ef92fcd2a50a85ed07
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Hur du kommer igång med Azure Table storage och Visual Studio anslutna tjänster

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Den här artikeln beskriver hur du kommer igång med Azure Table storage i Visual Studio när du har skapat eller refererar till ett Azure storage-konto i ASP.NET Core-projekt med hjälp av Visual Studio **anslutna tjänster** funktion. Den **anslutna tjänster** åtgärden installerar lämpligt NuGet-paket för att komma åt Azure-lagring i ditt projekt och lägger till anslutningssträngen för lagringskontot konfigurationsfilerna projektet. (Se [Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/) allmän information om Azure Storage.)

Tjänsten Azure Table storage kan du lagra stora mängder strukturerade data. Tjänsten är en NoSQL-databas som accepterar autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data. Mer allmän information om hur du använder Azure Table storage finns [komma igång med Azure Table storage med hjälp av .NET](../storage/storage-dotnet-how-to-use-tables.md).

Kom igång genom att först skapa en tabell i ditt lagringskonto. Den här artikeln visar hur du skapar en tabell i C# och hur du utför grundläggande tabellåtgärder som till exempel att lägga till, ändra, läsa och tar bort tabellposter i.  Koden använder Azure Storage-klientbiblioteket för .NET. Läs mer om ASP.NET [ASP.NET](http://www.asp.net).

Vissa av Azure Storage-API: er är asynkron och koden i den här artikeln förutsätter asynkrona metoder som används. Se [asynkron programmering](https://docs.microsoft.com/dotnet/csharp/async) för mer information.

## <a name="access-tables-in-code"></a>Åtkomst till tabeller i koden

För att komma åt tabeller i ASP.NET Core projekt måste du inkludera följande för att inga C# källfiler som kan komma åt Azure table storage.

1. Lägg till nödvändiga `using` instruktioner:

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Hämta en `CloudStorageAccount` objekt som representerar din kontoinformation för lagring. Använd följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en `CloudTableClient` objektet att referera till tabellen objekten i ditt lagringskonto:

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en `CloudTable` referensobjekt att referera till en viss tabell och enheter:

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Skapa en tabell i koden

Om du vill skapa Azure-tabellen anropa '' CreateIfNotExistsAsync()':

```cs
// Create the CloudTable if it does not exist
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

Om du vill lägga till en entitet i en tabell kan du skapa en klass som definierar egenskaperna för entiteten. Följande kod definierar en entitetsklass som kallas `CustomerEntity` som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel.

```cs
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Tabell åtgärder som rör entiteter används den `CloudTable` objekt du skapade tidigare i [åtkomst till tabeller i koden](#access-tables-in-code). Den `TableOperation` -objektet representerar åtgärden görs. Följande kodexempel visar hur du skapar en `CloudTable` objekt och en `CustomerEntity` objekt. Att förbereda åtgärden skapas en `TableOperation` skapas som infogar kundentiteten i tabellen. Slutligen körs åtgärden genom att anropa `CloudTable.ExecuteAsync`.

```cs
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter

Du kan infoga flera entiteter i en tabell i en enda skrivning. Följande exempel skapar två entitetsobjekt (”Jeff Smith” och ”Ben Smith”), läggs till i en `TableBatchOperation` objekt med hjälp av `Insert` -metoden och startar sedan åtgärden genom att anropa `CloudTable.ExecuteBatchAsync`.

```cs
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Hämta alla enheter i en partition

Om du vill fråga en tabell efter alla entiteter i en partition använder en `TableQuery` objekt. I följande kodexempel anges ett filter för entiteter där partitionsnyckeln är ”Smith”. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.

```cs
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Hämta en enda entitet

Du kan skriva en fråga om du vill ha en enda, specifik entitet. I följande kod används en `TableOperation` objekt för att ange en kund som heter ”Ben Smith”. Metoden returnerar endast en entitet i stället för en samling och det returnerade värdet i `TableResult.Result` är en `CustomerEntity` objekt. Ange både partitions- och nycklar i en fråga är det snabbaste sättet att hämta en enda entitet från den `Table` service.

```cs
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Ta bort en entitet

Du kan ta bort en enhet när du har hittat. Följande kod söker efter och tar bort en kundentitet med namnet ”Ben Smith”:

```cs
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
