---
title: Kom igång med Table Storage med hjälp av Visual Studio (ASP.NET Core)
description: Komma igång med Azure Table Storage i ett ASP.NET Core-projekt i Visual Studio efter anslutning till ett lagrings konto med hjälp av Visual Studio Connected Services
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
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298809"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Komma igång med Azure Table Storage och anslutna Visual Studio-tjänster

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Den här artikeln beskriver hur du kommer igång med Azure Table Storage i Visual Studio när du har skapat eller refererat till ett Azure Storage-konto i ett ASP.NET Core projekt med hjälp av funktionen Visual Studio **Connected Services** . Åtgärden **anslutna tjänster** installerar rätt NuGet-paket för att komma åt Azure Storage i projektet och lägger till anslutnings strängen för lagrings kontot i dina projekt konfigurationsfiler. (Se [lagrings dokumentation](https://azure.microsoft.com/documentation/services/storage/) för allmän information om Azure Storage.)

Med tjänsten Azure Table Storage kan du lagra stora mängder strukturerade data. Tjänsten är ett NoSQL data lager som accepterar autentiserade anrop inifrån och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data. Mer allmän information om hur du använder Azure Table Storage finns i [komma igång med Azure Table Storage med hjälp av .net](../storage/storage-dotnet-how-to-use-tables.md).

Kom igång genom att först skapa en tabell i ditt lagrings konto. Den här artikeln visar hur du skapar en tabell i C# och hur du utför grundläggande tabell åtgärder, till exempel att lägga till, ändra, läsa och ta bort tabell poster.  Koden använder Azure Storage klient biblioteket för .NET. Mer information om ASP.NET finns i [ASP.net](https://www.asp.net).

Några av de Azure Storage API: erna är asynkrona och koden i den här artikeln förutsätter att asynkrona metoder används. Mer information finns i [asynkron programmering](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-tables-in-code"></a>Komma åt tabeller i kod

Om du vill ha åtkomst till tabeller i ASP.NET Core-projekt måste du inkludera följande objekt C# i alla källfiler som har åtkomst till Azure Table Storage.

1. Lägg till nödvändiga `using`-uttryck:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Hämta ett `CloudStorageAccount`-objekt som representerar lagrings konto informationen. Använd följande kod med namnet på ditt lagrings konto och konto nyckeln, som du hittar i lagrings anslutnings strängen i appSettings. JSON:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Hämta ett `CloudTableClient`-objekt för att referera till tabell objekt i ditt lagrings konto:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett referens objekt för `CloudTable` för att referera till en speciell tabell och entiteter:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Skapa en tabell i kod

Om du vill skapa Azure-tabellen skapar du en asynkron metod och ringer `CreateIfNotExistsAsync()` i:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. Följande kod definierar en entitets klass med namnet `CustomerEntity` som använder kundens förnamn som rad nyckel och efter namn som partitionsnyckel.

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

Tabell åtgärder som involverar entiteter använder `CloudTable`-objektet som du skapade tidigare i [Access-tabeller i kod](#access-tables-in-code). Objektet `TableOperation` representerar den åtgärd som ska utföras. I följande kod exempel visas hur du skapar ett `CloudTable`-objekt och ett `CustomerEntity`-objekt. För att förbereda åtgärden skapas en `TableOperation` för att infoga kundentiteten i tabellen. Slutligen körs åtgärden genom att anropa `CloudTable.ExecuteAsync`.

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

Du kan infoga flera entiteter i en tabell i en enda Skriv åtgärd. Följande kod exempel skapar två entiteter ("Jeff Svensson" och "ben Svensson"), lägger till dem i ett `TableBatchOperation`-objekt med hjälp av metoden `Insert` och startar sedan åtgärden genom att anropa `CloudTable.ExecuteBatchAsync`.

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

Om du vill fråga en tabell efter alla entiteter i en partition använder du ett `TableQuery`-objekt. I följande kodexempel anges ett filter för entiteter där partitionsnyckeln är ”Smith”. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.

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

## <a name="get-a-single-entity"></a>Hämta en enskild entitet

Du kan skriva en fråga för att hämta en enskild, speciell entitet. I följande kod används ett `TableOperation`-objekt för att ange en kund med namnet "Ben Smith". Metoden returnerar bara en entitet i stället för en samling och det returnerade värdet i `TableResult.Result` är ett `CustomerEntity`-objekt. Att ange både partitions-och rad nycklar i en fråga är det snabbaste sättet att hämta en enskild entitet från tjänsten `Table`.

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

Du kan ta bort en entitet när du har hittat den. Följande kod söker efter och tar bort en kundentitet med namnet "ben Svensson":

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
