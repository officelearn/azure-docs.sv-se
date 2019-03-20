---
title: Kom igång med table storage och Visual Studio-anslutna tjänster (molntjänster) | Microsoft Docs
description: Hur du kommer igång med Azure Table storage i ett molntjänstprojekt i Visual Studio när du har anslutit till ett lagringskonto med hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: cec8ab9d678ff559176580fa8eccc261f449f4c5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012895"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Komma igång med Azure table storage och Visual Studio-anslutna tjänster (cloud services-projekt)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskrivs hur du kommer igång med Azure table storage i Visual Studio när du har skapat eller refererar till ett Azure storage-konto i ett cloud services-projekt med hjälp av Visual Studio **Lägg till Connected Services** dialogrutan. Den **Lägg till Connected Services** åtgärden installerar lämpligt NuGet-paket för att komma åt Azure storage i ditt projekt och lägger till anslutningssträngen för lagringskontot i konfigurationsfilerna projekt.

Azure Table storage-tjänsten kan du lagra stora mängder strukturerade data. Tjänsten är en NoSQL-datalager som tar emot autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.

För att komma igång, måste du först skapa en tabell i ditt storage-konto. Vi visar dig hur du skapar en Azure-tabell i koden och hur du utför grundläggande tabell och entiteten åtgärder, till exempel att lägga till, ändra, läsa och läsa tabellenheter. Exemplen är skrivna i C\# kod och använda den [Microsoft Azure Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Obs!** Vissa av de API: er som utför anrop ut till Azure storage är asynkrona. Se [asynkron programmering med Async och Await](https://msdn.microsoft.com/library/hh191443.aspx) för mer information. Koden nedan förutsätter asynkrona programming metoder som används.

* Se [komma igång med Azure Table storage med hjälp av .NET](../storage/storage-dotnet-how-to-use-tables.md) för mer information om hur du programmässigt arbete i tabeller.
* Se [dokumentation om Storage](https://azure.microsoft.com/documentation/services/storage/) allmän information om Azure Storage.
* Se [dokumentation om Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) allmän information om Azure-molntjänster.
* Se [ASP.NET](https://www.asp.net) för mer information om programmering i ASP.NET-program.

## <a name="access-tables-in-code"></a>Access-tabeller i kod
För att komma åt tabeller i molntjänstprojekt, måste du inkludera följande objekt till alla C# källfiler som har åtkomst till Azure-tabellagring.

1. Kontrollera att namnrymdsdeklarationer överst i C#-filen med dessa **med** instruktioner.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen av Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Använd all ovanstående kod framför kod som i följande exempel.
   > 
   > 
3. Hämta en **CloudTableClient** objekt för att referera till tabellobjekt i ditt storage-konto.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Hämta en **CloudTable** referensobjektet att referera till en viss tabell och entiteter.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Skapa en tabell i kod
Om du vill skapa Azure-tabell, ska du lägga till ett anrop till **CreateIfNotExistsAsync** till den när du har en **CloudTable** objekt enligt beskrivningen i avsnittet ”åt tabeller i kod”.

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. Följande kod definierar en entitetsklass som kallas **CustomerEntity** som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel.

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

Tabellåtgärder som rör entiteter utförs med hjälp av den **CloudTable** objekt som du skapade tidigare i ”Access-tabeller i kod”. Den **TableOperation** -objektet representerar åtgärden som ska utföras. I följande kodexempel visar hur du skapar en **CloudTable** objekt och en **CustomerEntity** objekt. Att förbereda åtgärden skapas en **TableOperation** infogar kundentiteten i tabellen. Slutligen körs åtgärden genom att anropa **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter
Du kan infoga flera entiteter i en tabell i en enda skrivåtgärd. I följande kodexempel skapar två entitetsobjekt (”Jeff Smith” och ”Ben Smith”), lägger till dem i en **TableBatchOperation** objekt med metoden Insert och startar sedan åtgärden genom att anropa  **CloudTable.ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Hämta alla entiteter i en partition
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
Du kan skriva en fråga för att hämta en enda, specifik entitet. I följande kod används en **TableOperation** objekt för att ange en kund med namnet ”Ben Smith”. Den här metoden returnerar endast en entitet i stället för en samling och värdet som returneras i **TableResult.Result** är en **CustomerEntity** objekt. Att ange både partition och radnycklar i en fråga är det snabbaste sättet att hämta en enda entitet från den **tabell** service.

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
Du kan ta bort en entitet när du har hittat. Följande kod som söker efter en kundentitet med namnet ”Ben Smith”, och om den hittar den tar bort den.

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

