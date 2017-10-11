---
title: "Kom igång med tabellagring och Visual Studio anslutna tjänster (molntjänster) | Microsoft Docs"
description: "Hur du kommer igång med Azure Table-lagring i en cloud service-projekt i Visual Studio efter anslutning till ett lagringskonto med hjälp av Visual Studio anslutna tjänster"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 51b71d783806d9b0d58d4473b8c07f77441dadd8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Komma igång med Azure-tabellagring och Visual Studio anslutna tjänster (cloud services-projekt)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kommer igång med Azure-tabellagring i Visual Studio när du har skapat eller refererar till ett Azure storage-konto i cloud services-projekt med hjälp av Visual Studio **Lägg till anslutna tjänster** dialogrutan. Den **Lägg till anslutna tjänster** åtgärden installerar lämpligt NuGet-paket för att komma åt Azure-lagring i ditt projekt och lägger till anslutningssträngen för lagringskontot konfigurationsfilerna projektet.

Tjänsten Azure Table storage kan du lagra stora mängder strukturerade data. Tjänsten är en NoSQL-datalager som tar emot autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.

Om du vill komma igång, måste du först skapa en tabell i ditt lagringskonto. Vi lära dig hur du skapar en Azure-tabellen i koden och hur du utför grundläggande tabell och entiteten åtgärder, till exempel lägga till, ändra, läsa och läsa tabellentiteter. Exemplen är skrivna i C\# code och använda den [Microsoft Azure Storage-klientbibliotek för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Obs:** några av de API: er som utför anrop ut till Azure storage är asynkron. Se [asynkron programmering med Async och Await](http://msdn.microsoft.com/library/hh191443.aspx) för mer information. Koden nedan förutsätter asynkrona programming metoder som används.

* Se [komma igång med Azure Table storage med hjälp av .NET](../storage/storage-dotnet-how-to-use-tables.md) mer information om programmässigt arbete i tabeller.
* Se [Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/) allmän information om Azure Storage.
* Se [molntjänster dokumentationen](https://azure.microsoft.com/documentation/services/cloud-services/) allmän information om Azure-molntjänster.
* Se [ASP.NET](http://www.asp.net) för mer information om programmering i ASP.NET-program.

## <a name="access-tables-in-code"></a>Åtkomst till tabeller i koden
Om du vill komma åt tabeller i molntjänstprojekt, måste du inkludera följande för att inga C# källfiler som kan komma åt Azure table storage.

1. Se till att namnrymdsdeklarationer överst i C#-filen med dessa **med** instruktioner.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfigurationen.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Du kan använda alla koden ovan framför koden i följande exempel.
   > 
   > 
3. Hämta en **CloudTableClient** objektet att referera till tabellen objekten i ditt lagringskonto.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Hämta en **CloudTable** referensobjekt att referera till en viss tabell och enheter.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Skapa en tabell i koden
Om du vill skapa Azure-tabellen, Lägg till ett anrop till **CreateIfNotExistsAsync** till den när du har fått en **CloudTable** objekt enligt beskrivningen i avsnittet ”kommer åt tabeller i kod”.

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. Följande kod definierar en entitetsklass som kallas **CustomerEntity** som använder kundens förnamn som Radnyckeln och efternamn som partitionsnyckel.

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

Tabellåtgärder som rör entiteter utförs med hjälp av den **CloudTable** objekt som du skapade tidigare i ”åtkomst tabeller i kod”. Den **TableOperation** -objektet representerar åtgärden görs. Följande kodexempel visar hur du skapar en **CloudTable** objekt och en **CustomerEntity** objekt. Att förbereda åtgärden skapas en **TableOperation** skapas som infogar kundentiteten i tabellen. Slutligen körs åtgärden genom att anropa **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter
Du kan infoga flera entiteter i en tabell i en enda skrivning. Följande exempel skapar två entitetsobjekt (”Jeff Smith” och ”Ben Smith”), läggs till i en **TableBatchOperation** objekt med metoden Insert och startar sedan åtgärden genom att anropa  **CloudTable.ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Hämta alla enheter i en partition
Om du vill fråga en tabell efter alla entiteter i en partition använder en **TableQuery** objekt. I följande kodexempel anges ett filter för entiteter där partitionsnyckeln är ”Smith”. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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

    return View();


## <a name="get-a-single-entity"></a>Hämta en enda entitet
Du kan skriva en fråga om du vill ha en enda, specifik entitet. I följande kod används en **TableOperation** objekt för att ange en kund som heter ”Ben Smith”. Den här metoden returnerar endast en entitet i stället för en samling och det returnerade värdet i **TableResult.Result** är en **CustomerEntity** objekt. Ange både partitions- och nycklar i en fråga är det snabbaste sättet att hämta en enda entitet från den **tabell** service.

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Ta bort en entitet
Du kan ta bort en enhet när du har hittat. Följande kod söker efter en kundentitet med namnet ”Ben Smith”, och om den hittar det tar bort den.

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

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

