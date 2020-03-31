---
title: Komma igång med tabelllagring med Visual Studio (ASP.NET Core)
description: Komma igång med Azure Table-lagring i ett ASP.NET Core-projekt i Visual Studio efter anslutning till ett lagringskonto med Hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: jillfra
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d209f8117b1e061877daf2f8d316bd01ed4f84cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298809"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Komma igång med Azure Table storage och Visual Studio-anslutna tjänster

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

I den här artikeln beskrivs hur du kommer igång med Azure Table storage i Visual Studio när du har skapat eller refererat till ett Azure-lagringskonto i ett ASP.NET Core-projekt med hjälp av Funktionen Visual Studio **Connected Services.** Åtgärden **Connected Services** installerar lämpliga NuGet-paket för att komma åt Azure-lagring i projektet och lägger till anslutningssträngen för lagringskontot i projektkonfigurationsfilerna. (Se [Lagringsdokumentation](https://azure.microsoft.com/documentation/services/storage/) för allmän information om Azure Storage.)

Med Azure Table Storage-tjänsten kan du lagra stora mängder strukturerad data. Tjänsten är ett NoSQL-datalager som accepterar autentiserade samtal från och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data. Mer allmän information om hur du använder Azure Table storage finns i [Komma igång med Azure Table storage med .NET](../storage/storage-dotnet-how-to-use-tables.md).

Kom igång genom att först skapa en tabell i ditt lagringskonto. Den här artikeln visar sedan hur du skapar en tabell i C# och hur du utför grundläggande tabellåtgärder som att lägga till, ändra, läsa och ta bort tabellposter.  Koden använder Azure Storage Client Library för .NET. Mer information om ASP.NET finns [i ASP.NET](https://www.asp.net).

Vissa azure storage API:er är asynkrona och koden i den här artikeln förutsätter att asynkrometoder används. Mer information [finns i Asynkron programmering.](https://docs.microsoft.com/dotnet/csharp/async)

## <a name="access-tables-in-code"></a>Komma åt tabeller i kod

Om du vill komma åt tabeller i ASP.NET Core-projekt måste du inkludera följande objekt till alla C#-källfiler som kommer åt Azure-tabelllagring.

1. Lägg till `using` nödvändiga satser:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Hämta `CloudStorageAccount` ett objekt som representerar din lagringskontoinformation. Använd följande kod med namnet på ditt lagringskonto och kontonyckeln, som du hittar i lagringsanslutningssträngen i appSettings.json:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Hämta `CloudTableClient` ett objekt som refererar till tabellobjekten i ditt lagringskonto:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta `CloudTable` ett referensobjekt för att referera till en viss tabell och entitet:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Skapa en tabell i kod

Om du vill skapa Azure-tabellen skapar du `CreateIfNotExistsAsync()`en asynkron metod och anropar den i den:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för din entitet. Följande kod definierar en `CustomerEntity` entitetsklass som anropas och som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel.

```csharp
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

Tabellåtgärder som involverar `CloudTable` entiteter använder objektet som du skapade tidigare i [Access-tabeller i kod](#access-tables-in-code). Objektet `TableOperation` representerar den åtgärd som ska utföras. I följande kodexempel `CloudTable` visas hur `CustomerEntity` du skapar ett objekt och ett objekt. För att förbereda `TableOperation` operationen skapas en för att infoga kundentiteten i tabellen. Slutligen utförs åtgärden genom att `CloudTable.ExecuteAsync`anropa .

```csharp
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

Du kan infoga flera entiteter i en tabell i en enda skrivåtgärd. I följande kodexempel skapas två entitetsobjekt ("Jeff `TableBatchOperation` Smith" `Insert` och "Ben Smith"), `CloudTable.ExecuteBatchAsync`läggs dem till i ett objekt med metoden och startar sedan åtgärden genom att anropa .

```csharp
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

## <a name="get-all-of-the-entities-in-a-partition"></a>Hämta alla entiteter i en partition

Om du vill fråga en tabell efter alla `TableQuery` entiteter i en partition använder du ett objekt. I följande kodexempel anges ett filter för entiteter där partitionsnyckeln är ”Smith”. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.

```csharp
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

## <a name="get-a-single-entity"></a>Skaffa en enda entitet

Du kan skriva en fråga för att få en enda specifik entitet. Följande kod använder `TableOperation` ett objekt för att ange en kund med namnet "Ben Smith". Metoden returnerar bara en entitet i stället för `TableResult.Result` en `CustomerEntity` samling, och det returnerade värdet i är ett objekt. Att ange både partitions- och radnycklar i en fråga `Table` är det snabbaste sättet att hämta en enda entitet från tjänsten.

```csharp
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

Du kan ta bort en entitet när du har hittat den. Följande kod söker efter och tar bort en kundentitet med namnet "Ben Smith":

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

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
