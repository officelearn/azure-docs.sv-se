---
title: "Komma igång med Azure Table Storage med hjälp av .NET | Microsoft Docs"
description: "Lagra strukturerade data i molnet med hjälp av Azure Table Storage, en NoSQL-databas."
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 7a9a28ce8be7587c84a1188d643c990cc4fb7355
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-table-storage-using-net"></a>Komma igång med Azure Table Storage med hjälp av .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Översikt
Azure Table Storage är en tjänst som lagrar strukturerade NoSQL-data i molnet. Table Storage är en nyckel- och attributdatabas med en schemalös design. Eftersom Table Storage är schemalös är det enkelt att anpassa dina data i takt med att programmets behov förändras. Åtkomsten till data är snabb och kostnadseffektiv för alla typer av program. Kostnaden för Table Storage är normalt sett betydligt lägre än för motsvarande volymer med traditionell SQL.

Du kan använda Table Storage för att lagra flexibla datauppsättningar, till exempel användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

### <a name="about-this-tutorial"></a>Om den här självstudiekursen
I den här kursen lär du dig hur du skriver .NET-kod för några vanliga scenarier med hjälp av Azure Table Storage. Du lär dig bland annat hur du skapar och tar bort en tabell och hur du infogar, uppdaterar, tar bort och frågar tabelldata.

**Förutsättningar:**

* [Microsoft Visual Studio](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)
* [Azure Storage-klientbibliotek för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Azure Configuration Manager för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* Ett [Azure Storage-konto](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Fler exempel
Ytterligare exempel med Table Storage finns i [Komma igång med Azure Table Storage i .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Du kan ladda ned exempelprogrammet och köra det eller bläddra i koden på GitHub.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Lägga till med hjälp av direktiv
Lägg till följande **using**-direktiv längst upp i filen `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Skapa tabelltjänstens klient
Med klassen **CloudTableClient** kan du hämta tabeller och de entiteter som lagras i Table Storage. Här är ett sätt att skapa tjänstklienten:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Nu är det dags att skriva kod som läser data från och skriver data till Table Storage.

## <a name="create-a-table"></a>Skapa en tabell
Det här exemplet illustrerar hur du skapar en tabell om den inte redan finns:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Entiteter mappar till C\#-objekt med hjälp av en anpassad klass som härleds från **TableEntity**. Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. Följande kod definierar en entitetsklass som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Det går snabbare att fråga entiteter med samma partitionsnyckel än entiteter som har olika partitionsnycklar, men skalbarheten och möjligheten att utföra parallella åtgärder är större med olika partitionsnycklar. För egenskaper som ska lagras i tabelltjänsten måste egenskapen vara en offentlig egenskap för en typ som stöds och som exponerar både inställnings- och hämtningsvärden.
Dessutom *måste* entitetstypen exponera en parameterlös konstruktor.

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

Tabellåtgärder som rör entiteter utförs via **CloudTable**-objektet som du skapade tidigare i avsnittet Skapa en tabell. Åtgärden som ska utföras representeras av ett **TableOperation**-objekt.  Följande kodexempel visar hur **CloudTable**-objektet skapas och sedan ett **CustomerEntity**-objekt.  För att förbereda åtgärden skapas ett **TableOperation**-objekt som infogar kundentiteten i tabellen.  Slutligen körs åtgärden genom att **CloudTable.Execute** anropas.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter
Du kan infoga en batch med entiteter i en tabell i samma skrivåtgärd. Några anmärkningar om batchåtgärder:

* Du kan utföra uppdateringar, borttagningar och infogningar i samma batchåtgärd.
* En batchåtgärd kan omfatta upp till 100 entiteter.
* Alla entiteter i samma batchåtgärd måste ha samma partitionsnyckel.
* Även om det går att köra en fråga som en batchåtgärd måste den vara den enda åtgärden i batchen.

<!-- -->
Följande kod skapar två entitetsobjekt och lägger till vart och ett i **TableBatchOperation** med hjälp av **Insert**-metoden. Därefter anropas **CloudTable.Execute** för att köra åtgärden.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

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
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition
Om du vill fråga en tabell efter alla entiteter i en partition använder du ett **TableQuery**-objekt.
I följande kodexempel anges ett filter för entiteter där partitionsnyckeln är ”Smith”. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Hämta ett intervall med enheter i en partition
Om du inte vill fråga efter alla entiteter i en partition kan du ange ett intervall genom att kombinera partitionsnyckelfiltret med ett radnyckelfilter. I följande kodexempel används två filter för att hämta alla entiteter i partitionen ”Smith” där radnyckeln (förnamn) börjar med en bokstav som kommer före ”E” i alfabetet, varefter frågeresultatet skrivs ut.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>Hämta en enda entitet
Du kan skriva en fråga för att hämta en enda, specifik entitet. I följande kod används **TableOperation** för att ange kunden ”Ben Smith”.
Den här metoden returnerar endast en entitet i stället för en samling, och värdet som returneras i **TableResult.Result** är ett **CustomerEntity**-objekt.
Det snabbaste sättet att hämta en enskild entitet från tabelltjänsten är att ange både partitions- och radnycklar i en fråga.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Ersätta en entitet
Om du vill uppdatera en entitet hämtar du den från tabelltjänsten, ändrar entitetsobjektet och sparar sedan ändringarna till tabelltjänsten igen. Följande kod ändrar en befintlig kunds telefonnummer. I stället för att anropa **Insert** använder den här koden **Replace**. Detta leder till att entiteten ersätts helt på servern, såvida inte entiteten på servern har ändrats sedan den hämtades. I så fall misslyckas åtgärden.  Det här felet är avsett att förhindra att ditt program oavsiktligt skriver över ändringar mellan hämtningen och uppdateringen av en annan komponent i ditt program.  Du hanterar det här felet genom att hämta entiteten igen, göra dina ändringar (om de fortfarande behövs) och sedan köra en **Replace**-åtgärd igen.  I nästa avsnitt visar vi hur du kan åsidosätta detta beteende.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Infoga eller ersätta en entitet
**Replace**-åtgärder misslyckas om entiteten har ändrats sedan den hämtades från servern.  Dessutom måste du hämta entiteten från servern först för att **Replace**-åtgärden ska lyckas.
Ibland vet du dock inte om entiteten finns på servern, och vilka värden som för närvarande lagras i den är irrelevanta. Dina uppdateringar ska skriva över alla.  För att åstadkomma detta använder du en **InsertOrReplace**-åtgärd.  Den här åtgärden infogar entiteten om den inte finns, eller ersätter den om den finns, oavsett när den senaste uppdateringen gjordes.  I följande kodexempel hämtas fortfarande kundentiteten för Ben Still, men den sparas sedan tillbaka till servern via **InsertOrReplace**.  Uppdateringar som görs i entiteten mellan hämtnings- och uppdateringsåtgärderna skrivs över.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-1234";

    // Create the InsertOrReplace TableOperation.
    TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

    // Execute the operation.
    table.Execute(insertOrReplaceOperation);

    Console.WriteLine("Entity was updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper
En tabellfråga kan hämta bara några få egenskaper från en entitet i stället för alla entitetsegenskaper. Den här tekniken, kallad projektion, minskar bandbredden och kan förbättra frågeprestanda, i synnerhet för stora entiteter. Frågan i följande kod returnerar bara e-postadresserna för entiteter i tabellen. Detta görs med hjälp av en fråga med **DynamicTableEntity** och **EntityResolver**. Du kan lära dig mer om projektion i blogginlägget [Introducing Upsert and Query Projection][Introducing Upsert and Query Projection blog post]. Observera att projektion inte stöds i den lokala lagringsemulatorn. Det betyder att den här koden endast körs när du använder ett konto i tabelltjänsten.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Ta bort en entitet
Du kan enkelt ta bort en enhet när du har hämtat den genom att använda samma mönster som när du uppdaterar en entitet.  Följande kod hämtar och tar bort en kundentitet.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Ta bort en tabell
I det sista kodexemplet tas en tabell bort från ett lagringskonto. En tabell som har tagits bort kan inte återskapas under en viss tid efter borttagningen.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Hämta entiteter på sidor asynkront
Om du läser ett stort antal entiteter och du vill bearbeta/visa entiteter i takt med att de hämtas i stället för att vänta på att alla ska returneras kan du hämta entiteter med hjälp av en segmenterad fråga. Det här exemplet visar hur du returnerar resultat på sidor med mönstret Async-Await så att körningen inte blockeras medan du väntar på att ett stort antal resultat ska returneras. Mer information om hur du använder mönstret Async-Await i .NET finns i [Asynkron programmering med Async och Await (C# och Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Table Storage kan du följa dessa länkar för att lära dig mer om komplexa lagringsuppgifter:

* Du hittar fler Table Storage-exempel i [Komma igång med Azure Table Storage i .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* Fullständig information om tillgängliga API:er finns i referensdokumentationen för tabelltjänsten:
  * [Storage-klientbibliotek för .NET-referens](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [REST API-referens](http://msdn.microsoft.com/library/azure/dd179355)
* Lär dig hur du förenklar koden du skriver så att den fungerar med Azure Storage genom att använda [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
* Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.
  * [Kom igång med Azure Blob Storage med hjälp av .NET](storage-dotnet-how-to-use-blobs.md) om du vill lagra ostrukturerade data.
  * [Anslut till SQL Database med hjälp av .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) för att lagra relationsdata.

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
[Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
[Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
[Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
[Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

[Introducing Upsert and Query Projection blog post]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
[OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
[How to: Programmatically access Table storage]: #tablestorage

