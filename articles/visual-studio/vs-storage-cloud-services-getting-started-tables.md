---
title: Komma igång med tabelllagring med Visual Studio (molntjänster)
description: Komma igång med Azure Table-lagring i ett molntjänstprojekt i Visual Studio efter anslutning till ett lagringskonto med Hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c42d65b5e2c46fcdbe1b0725f2ebce881722db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299986"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Komma igång med Azure-tabelllagring och Anslutna Visuell studio-tjänster (molntjänstprojekt)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt
I den här artikeln beskrivs hur du kommer igång med Azure-tabelllagring i Visual Studio när du har skapat eller refererat till ett Azure-lagringskonto i ett molntjänstprojekt med hjälp av dialogrutan **Lägg till anslutna tjänster i** Visual Studio. Åtgärden **Lägg till anslutna tjänster** installerar lämpliga NuGet-paket för att komma åt Azure-lagring i projektet och lägger till anslutningssträngen för lagringskontot i projektkonfigurationsfilerna.

Med Azure Table Storage-tjänsten kan du lagra stora mängder strukturerad data. Tjänsten är ett NoSQL-datacenter som accepterar autentiserade samtal från och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.

För att komma igång måste du först skapa en tabell i ditt lagringskonto. Vi visar dig hur du skapar en Azure-tabell i kod och även hur du utför grundläggande tabell- och entitetsåtgärder, till exempel lägga till, ändra, läsa och läsa tabellentiteter. Exemplen skrivs\# i C-kod och använder [Microsoft Azure Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**OBS:** Vissa API:er som utför anrop till Azure-lagring är asynkrona. Se [Asynkron programmering med Async och Vänta för](https://msdn.microsoft.com/library/hh191443.aspx) mer information. Koden nedan förutsätter async programmeringsmetoder används.

* Mer information om programmatiskt manipulera tabeller [finns i Komma igång med Azure Table-lagring med .NET.](../storage/storage-dotnet-how-to-use-tables.md)
* Mer information om Azure Storage finns i Dokumentation för [lagring av](https://azure.microsoft.com/documentation/services/storage/) lagring.
* Mer information om Azure-molntjänster finns i Dokumentationen till [Molntjänster](https://azure.microsoft.com/documentation/services/cloud-services/) för Molntjänster.
* Mer information om programmering ASP.NET program finns [i ASP.NET.](https://www.asp.net)

## <a name="access-tables-in-code"></a>Komma åt tabeller i kod
Om du vill komma åt tabeller i molntjänstprojekt måste du inkludera följande objekt till alla C#-källfiler som kommer åt Azure-tabelllagring.

1. Kontrollera att namnområdesdeklarationerna högst upp i C#-filen innehåller dessa **med hjälp av** satser.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Hämta ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Använd alla ovanstående kod framför koden i följande exempel.
   > 
   > 
3. Skaffa ett **CloudTableClient-objekt** för att referera till tabellobjekten i ditt lagringskonto.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Hämta ett **CloudTable-referensobjekt** för att referera till en viss tabell och entiteter.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Skapa en tabell i kod
Om du vill skapa Azure-tabellen lägger du bara till ett anrop i **CreateIfNotExistsAsync** till när du har fått ett **CloudTable-objekt** enligt beskrivningen i avsnittet "Åtkomsttabeller i kod".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. Följande kod definierar en entitetsklass som kallas **CustomerEntity** som använder kundens förnamn som radnyckel och efternamnet som partitionsnyckel.

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

Tabellåtgärder som involverar entiteter utförs med det **CloudTable-objekt** som du skapade tidigare i "Access-tabeller i kod". **TableOperation-objektet** representerar den åtgärd som ska utföras. I följande kodexempel visas hur du skapar ett **CloudTable-objekt** och ett **CustomerEntity-objekt.** För att förbereda operationen skapas en **TableOperation** för att infoga kundentiteten i tabellen. Slutligen körs åtgärden genom att anropa **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter
Du kan infoga flera entiteter i en tabell i en enda skrivåtgärd. I följande kodexempel skapas två entitetsobjekt ("Jeff Smith" och "Ben Smith"), lägger till dem i ett **TableBatchOperation-objekt** med metoden Infoga och startar sedan åtgärden genom att anropa **CloudTable.ExecuteBatchAsync**.

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
Om du vill fråga en tabell efter alla entiteter i en partition använder du ett **TableQuery-objekt.** I följande kodexempel anges ett filter för entiteter där partitionsnyckeln är ”Smith”. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.

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


## <a name="get-a-single-entity"></a>Skaffa en enda entitet
Du kan skriva en fråga för att få en enda specifik entitet. Följande kod använder ett **TableOperation-objekt** för att ange en kund med namnet "Ben Smith". Den här metoden returnerar bara en entitet i stället för en samling, och det returnerade värdet i **TableResult.Result** är ett **CustomerEntity-objekt.** Att ange både partitions- och radnycklar i en fråga är det snabbaste sättet att hämta en enda entitet från **tabelltjänsten.**

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
Du kan ta bort en entitet när du har hittat den. Följande kod söker efter en kundentitet med namnet "Ben Smith", och om den hittar den tas den bort.

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

