---
title: Kom igång med Azure Cosmos DB Table API med hjälp av SDK för .NET Standard
description: Store strukturerade data i molnet med Azure Cosmos DB Table API.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 03/11/2019
ms.openlocfilehash: 0a329722b65e407f011016a1f55e86ef17b47d70
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192406"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Komma igång med Azure Cosmos DB Table API och Azure Table storage med .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Du kan använda Azure Cosmos DB Table API eller Azure Table storage för att lagra strukturerade NoSQL data i molnet, vilket ger ett nyckelattribut lagra med ett schema mindre design. Eftersom Azure Cosmos DB Table API och Table storage är schemat mindre, är det enkelt att anpassa dina data efter behov av utvecklas. Du kan använda Azure Cosmos DB Table API eller Table storage för att lagra flexibla datauppsättningar som användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver. 

Den här självstudien beskrivs ett exempel som visar hur du använder den [Microsoft Azure Cosmos DB tabell-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) med Azure Cosmo DB Table API och Azure Table storage-scenarier. Du måste använda anslutningen som är specifika för Azure-tjänsten. Dessa scenarier beskrivs med hjälp av C# exempel som visar hur du skapar tabeller, infoga / uppdatera data, fråga efter data och ta bort tabellerna.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att kunna följa med i det här exemplet:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure cosmos DB tabell-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) – det här biblioteket är för närvarande tillgängligt för .NET Standard- och .NET framework. 

* [Azure Cosmos DB Table API-konto](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett konto för Table-API:et för Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Skapa ett projekt för .NET-konsolen

Skapa ett nytt .NET-konsolprogram i Visual Studio. Följande steg visar hur du skapar ett konsolprogram i Visual Studio 2017. Stegen är ungefär som i andra versioner av Visual Studio. Du kan använda Azure Cosmos DB tabell-klientbiblioteket i alla typer av .NET-program, inklusive en Azure-molnet tjänsten eller web app, och stationära och mobila program. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

1. Välj **Arkiv** > **Nytt** > **Projekt**.

1. Välj **installerat** > **Visual C#**   >  **konsolprogram (.NET Core)**.

1. I den **namn** fältet, anger du ett namn för ditt program, till exempel **CosmosTableSamples** (du kan ange ett annat namn efter behov).

1. Välj **OK**.

Alla kodexempel i det här exemplet kan läggas till Main()-metoden i konsolprogrammets **Program.cs** fil.

## <a name="install-the-required-nuget-package"></a>Installera NuGet-paket som krävs

Du kan hämta NuGet-paketet genom att följa dessa steg:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.

1. Sök online efter `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json`, `Microsoft.Extensions.Configuration.Binder` och välj **installera** att installera Microsoft Azure Cosmos DB tabell-biblioteket.

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

1. Från den [Azure-portalen](https://portal.azure.com/), klickar du på **Connection String**. Använd knapparna på höger sida av fönstret för att kopiera **PRIMÄR ANSLUTNINGSSTRÄNG**.

   ![Visa och kopiera PRIMÄR ANSLUTNINGSSTRÄNG i fönstret Anslutningssträng](./media/create-table-dotnet/connection-string.png)
   
1. Om du vill konfigurera anslutningssträngen från visual studio högerklickar du på projektet **CosmosTableSamples**.

1. Välj **lägga till** och sedan **nytt objekt**. Skapa en ny fil **Settings.json** med filtyp som **TypeScript JSON Configuration** fil. 

1. Ersätt Koden i filen Settings.json med följande kod och tilldela din primär anslutningssträng:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Högerklicka på projektet **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och lägga till en klass med namnet **AppSettings.cs**.

1. Lägg till följande kod i filen AppSettings.cs. Den här filen läser anslutningssträngen från filen Settings.json och tilldelar den Konfigurationsparametern:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Parsa och kontrollera anslutningsinformationen 

1. Högerklicka på projektet **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och lägga till en klass med namnet **Common.cs**. Du skriver kod för att verifiera anslutningsinformationen och skapar en tabell i den här klassen.

1. Definiera en metod `CreateStorageAccountFromConnectionString` enligt nedan. Den här metoden Parsar sträng anslutningsinformationen och kontrollera att kontonamnet och viktig information om kontot som finns i filen ”Settings.json” är giltiga. 

   ```csharp
   public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
    {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
   ```


## <a name="create-a-table"></a>Skapa en tabell 

Med klassen [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) kan du hämta tabeller och de entiteter som lagras i Table Storage. Eftersom det inte finns några tabeller i Cosmos DB Table API-konto, ska vi lägga till den `CreateTableAsync` metod för att den **Common.cs** klassen för att skapa en tabell:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

## <a name="define-the-entity"></a>Definiera entiteten 

Entiteter mappar till C# objekt med hjälp av en anpassad klass som härleds från [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten.

Högerklicka på projektet **CosmosTableSamples**. Välj **Lägg till**, **ny mapp** och ger den namnet **modellen**. Lägg till en klass med namnet i mappen modellen **CustomerEntity.cs** och Lägg till följande kod till den.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Den här koden definierar en entitetsklass som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel den unikt i tabellen. Entiteter med samma partitionsnyckel kan frågas snabbare än entiteter med olika partitionsnycklar, men med olika partitionsnycklar ger bättre skalbarhet parallella åtgärder. Enheter som ska lagras i tabeller måste vara av en typ som stöds, till exempel härledda från den [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) klass. Entitetsegenskaper som du vill lagra i en tabell måste vara offentliga egenskaper av den typen och ha stöd för att både hämta och ange värden. Dessutom måste entitetstypen exponera en parameterlös konstruktor.

## <a name="insert-or-merge-an-entity"></a>Infoga eller slå samman en entitet

Följande exempel skapar ett entitetsobjekt och lägger till den i tabellen. Metoden InsertOrMerge inom den [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) klass används för att infoga eller slå samman en entitet. Den [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) metoden anropas för att köra åtgärden. 

Högerklicka på projektet **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och lägga till en klass med namnet **SamplesUtils.cs**. Den här klassen lagrar all kod som krävs för att utföra CRUD-åtgärder på entiteter. 

```csharp
public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
    {
      if (entity == null)
    {
       throw new ArgumentNullException("entity");
    }
    try
    {
       // Create the InsertOrReplace table operation
       TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

       // Execute the operation.
       TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
       CustomerEntity insertedCustomer = result.Result as CustomerEntity;
        
        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
          {
            Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
          }

        return insertedCustomer;
        }
        catch (StorageException e)
        {
          Console.WriteLine(e.Message);
          Console.ReadLine();
          throw;
        }
    }
```

### <a name="get-an-entity-from-a-partition"></a>Hämta en entitet från en partition

Du kan hämta entiteten från en partition med hjälp av metoden hämta under den [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) klass. Följande exempel hämtar rad partitionsnyckel, e-post och telefon antal en kundentitet. Det här exemplet skriver också ut enheter för programbegäran som används för att fråga efter entiteten. Lägg till följande kod för att fråga efter en entitet, **SamplesUtils.cs** fil: 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Ta bort en entitet

Du kan enkelt ta bort en enhet när du har hämtat den genom att använda samma mönster som när du uppdaterar en entitet. Följande kod hämtar och tar bort en kundentitet. Lägg till följande kod för att ta bort en entitet, **SamplesUtils.cs** fil: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Utför CRUD-åtgärder i exempeldata

När du definierar metoder för att skapa tabell-, insert- eller merge-entiteter, köra dessa metoder på exempeldata. Om du vill göra det högerklickar du på projektet **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och lägga till en klass med namnet **BasicSamples.cs** och Lägg till följande kod till den. Den här koden skapar en tabell, lägger till enheter i den. Om du vill ta bort den entiteten och tabellen i slutet av projektet kan du ta bort eventuella kommentarer från `table.DeleteIfExistsAsync()` och `SamplesUtils.DeleteEntityAsync(table, customer)` metoder från följande kod:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Den tidigare koden skapar en tabell som börjar med ”demo” och det GUID som genererats läggs till i tabellens namn. Den lägger sedan till en kundentitet med första och sista namn som ”Walter Harp” och senare uppdaterar telefonnumret till den här användaren. 

I den här självstudien har skapat du kod för att utföra grundläggande CRUD-åtgärder på data som lagras i tabell-API-konto. Du kan också utföra avancerade åtgärder som – batchdata för att infoga, fråga alla data inom en partition fråga en mängd data inom en partition, en lista över tabeller i kontot vars namn börjar med det angivna prefixet. Du kan ladda ned hela exemplet formuläret [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub-lagringsplatsen. Den [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) klassen har flera åtgärder som du kan utföra på data.  

## <a name="run-the-project"></a>Kör projektet

Nu skapa lösningen och tryck på F5 för att köra projektet. När projektet har körts visas följande utdata i Kommandotolken:

![Utdata från Kommandotolken](./media/tutorial-develop-table-standard/output-from-sample.png)

Om du får ett meddelande om att det inte går att hitta filen Settings.json när du kör projektet, kan du lösa det genom att lägga till följande XML-post för projektet. Högerklicka på CosmosTableSamples, Välj Redigera CosmosTableSamples.csproj och Lägg till följande itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Du kan nu logga in på Azure portal och kontrollera att det finns data i tabellen. 

![Resultat i portalen](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta till nästa självstudie och lär dig hur du migrerar data till Azure Cosmos DB Table API-konto. 

> [!div class="nextstepaction"]
>[Hur du frågar efter data](../cosmos-db/table-import.md)
