---
title: Azure Cosmos DB-tabell-API med .NET Standard SDK
description: Lär dig hur du lagrar och frågar strukturerade data i Azure Cosmos DB Table API-konto
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: f4d6e1bb0d5db0dbfc30e14abc58321bce8d0baf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238456"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Kom igång med tabell-API för Azure Cosmos DB och Azure Table Storage med .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Du kan använda Azure Cosmos DB Table API eller Azure Table Storage för att lagra strukturerade NoSQL-data i molnet, vilket ger ett nyckel-/attributarkiv med ett schema mindre design. Eftersom Azure Cosmos DB Table API och Tabelllagring är schema mindre, är det enkelt att anpassa dina data när behoven i ditt program utvecklas. Du kan använda Azure Cosmos DB Table API eller Tabelllagring för att lagra flexibla datauppsättningar som användardata för webbprogram, adressböcker, enhetsinformation eller andra typer av metadata som tjänsten kräver. 

I den här självstudien beskrivs ett exempel som visar hur du använder [Microsoft Azure Cosmos DB Table Library för .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) med Azure Cosmos DB Table API och Azure Table Storage scenarios. Du måste använda anslutningen som är specifik för Azure-tjänsten. Dessa scenarier utforskas med hjälp av C# exempel som illustrerar hur du skapar tabeller, infoga / uppdatera data, frågedata och ta bort tabellerna.

## <a name="prerequisites"></a>Krav

Du behöver följande för att kunna följa med i det här exemplet:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB Table Library för .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) - Det här biblioteket är för närvarande tillgängligt för .NET Standard och .NET framework. 

* [Api-kontot för Azure Cosmos DB-tabell](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett Azure Cosmos DB Table API-konto

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Skapa ett .NET-konsolprojekt

Skapa ett nytt .NET-konsolprogram i Visual Studio. Följande steg visar hur du skapar ett konsolprogram i Visual Studio 2019. Du kan använda Azure Cosmos DB Table Library i alla typer av .NET-program, inklusive en Azure-molntjänst eller webbapp och skrivbords- och mobilappar. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

1. Välj **Arkiv** > **Nytt** > **projekt**.

1. Välj **Console App (.NET Core)** och välj sedan **Nästa**.

1. Ange ett namn på programmet i fältet **Projektnamn,** till exempel **CosmosTableSamples**. (Du kan ange ett annat namn efter behov.)

1. Välj **Skapa**.

Alla kodexempel i det här exemplet kan läggas till **Program.cs** i Main(-metoden för konsolprogrammets Program.cs-fil.

## <a name="install-the-required-nuget-package"></a>Installera det Nödvändiga NuGet-paketet

Så här hämtar du NuGet-paketet:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.

1. Sök online [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)efter [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) , , och välj **Installera** för att installera Microsoft Azure Cosmos DB Table Library.

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

1. Från [Azure-portalen](https://portal.azure.com/)navigerar du till ditt Azure Cosmos-konto eller Table Storage-kontot. 

1. Öppna fönstret **Anslutningssträng** eller **Snabbtangenter.** Använd knapparna på höger sida av fönstret för att kopiera **PRIMÄR ANSLUTNINGSSTRÄNG**.

   ![Visa och kopiera PRIMÄR ANSLUTNINGSSTRÄNG i fönstret Anslutningssträng](./media/create-table-dotnet/connection-string.png)
   
1. För att konfigurera din anslutningssträng, från visual studio högerklicka på ditt projekt **CosmosTableSamples**.

1. Välj **Lägg till** och sedan Nytt **objekt**. Skapa en ny fil **Settings.json** med filtyp som **TypeScript JSON Configuration** File. 

1. Ersätt koden i filen Settings.json med följande kod och tilldela den primära anslutningssträngen:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Högerklicka på ditt projekt **CosmosTableSamples**. Välj **Lägg till**, Nytt **objekt** och lägg till en klass med namnet **AppSettings.cs**.

1. Lägg till följande kod i AppSettings.cs filen. Den här filen läser anslutningssträngen från Filen Settings.json och tilldelar den till konfigurationsparametern:

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

## <a name="parse-and-validate-the-connection-details"></a>Tolka och validera anslutningsinformation 

1. Högerklicka på ditt projekt **CosmosTableSamples**. Välj **Lägg till**, Nytt **objekt** och lägg till en klass med namnet **Common.cs**. Du skriver kod för att validera anslutningsinformationen och skapa en tabell i den här klassen.

1. Definiera en `CreateStorageAccountFromConnectionString` metod som visas nedan. Den här metoden tolkar information om anslutningssträngen och verifierar att kontonamn och kontonyckelinformation som anges i filen "Settings.json" är giltiga. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
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
    }
}
   ```


## <a name="create-a-table"></a>Skapa en tabell 

Med klassen [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) kan du hämta tabeller och de entiteter som lagras i Table Storage. Eftersom vi inte har några tabeller i API-kontot för Cosmos `CreateTableAsync` DB Table ska vi lägga till metoden i **klassen Common.cs** för att skapa en tabell:

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

Om du får felmeddelandet "503-tjänsten är inte tillgänglig" är det möjligt att de nödvändiga portarna för anslutningsläget blockeras av en brandvägg. Lös problemet genom att antingen öppna de portar som krävs eller använda gateway-lägesanslutningen enligt följande kod:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Definiera entiteten 

Entiteter mappar till C#-objekt med hjälp av en anpassad klass som härleds från [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten.

Högerklicka på ditt projekt **CosmosTableSamples**. Välj **Lägg till**, Ny **mapp** och namnge den som **modell**. Lägg till en klass med namnet **CustomerEntity.cs** i mappen Modell och lägg till följande kod i den.

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

Den här koden definierar en entitetsklass som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel den unikt i tabellen. Entiteter med samma partitionsnyckel kan efterfrågas snabbare än entiteter med olika partitionsnycklar, men med hjälp av olika partitionsnycklar kan parallella åtgärder skalas större. Enheter som ska lagras i tabeller måste ha en typ som stöds, till exempel härledda från klassen [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Entitetsegenskaper som du vill lagra i en tabell måste vara offentliga egenskaper av den typen och ha stöd för att både hämta och ange värden. Dessutom måste entitetstypen exponera en parameterlös konstruktor.

## <a name="insert-or-merge-an-entity"></a>Infoga eller sammanfoga en entitet

I följande kodexempel skapas ett entitetsobjekt och det läggs till i tabellen. Metoden InsertOrMerge i klassen [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) används för att infoga eller sammanfoga en entitet. [Metoden CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) anropas för att köra åtgärden. 

Högerklicka på ditt projekt **CosmosTableSamples**. Välj **Lägg till**, Nytt **objekt** och lägg till en klass med namnet **SamplesUtils.cs**. Den här klassen lagrar all kod som krävs för att utföra CRUD-åtgärder på entiteterna. 

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

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
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

Du kan hämta entiteten från en partition med hjälp av metoden Hämta under klassen [TableOperation.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) Följande kodexempel hämtar radnyckeln för partitionsnyckeln, e-post- och telefonnumret till en kundentitet. I det här exemplet skrivs också ut de begärandeenheter som förbrukats för att fråga efter entiteten. Om du vill fråga efter en entitet lägger du till följande kod för att **SamplesUtils.cs** filen: 

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

Du kan enkelt ta bort en enhet när du har hämtat den genom att använda samma mönster som när du uppdaterar en entitet. Följande kod hämtar och tar bort en kundentitet. Om du vill ta bort en **SamplesUtils.cs** entitet lägger du till följande kod i SamplesUtils.cs-filen: 

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

## <a name="execute-the-crud-operations-on-sample-data"></a>Utföra CRUD-åtgärder på exempeldata

När du har definierat metoderna för att skapa tabell, infoga eller sammanfoga entiteter kör du dessa metoder på exempeldata. För att göra det, högerklicka på ditt projekt **CosmosTableSamples**. Välj **Lägg till**, Nytt **objekt** och lägg till en klass med namnet **BasicSamples.cs** och lägg till följande kod i den. Den här koden skapar en tabell, lägger till entiteter i den. Om du vill ta bort entiteten och tabellen i `table.DeleteIfExistsAsync()` `SamplesUtils.DeleteEntityAsync(table, customer)` slutet av projektet tar du bort kommentarerna från och metoderna från följande kod:

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

Den tidigare koden skapar en tabell som börjar med "demo" och det genererade GUID läggs till i tabellnamnet. Den lägger sedan till en kundenhet med för- och efternamn som "Harpa Walter" och uppdaterar senare den här användarens telefonnummer. 

I den här självstudien har du skapat kod för att utföra grundläggande CRUD-åtgärder på data som lagras i tabell-API-konto. Du kan också utföra avancerade åtgärder som – batchinfogning av data, fråga alla data i en partition, fråga ett dataområde inom en partition, Visar tabeller i kontot vars namn börjar med det angivna prefixet. Du kan hämta det fullständiga exempelformuläret [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub-databasen. Klassen [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) har fler åtgärder som du kan utföra på data.  

## <a name="run-the-project"></a>Kör projektet

Från projektet **CosmosTableSamples**. Öppna klassen **med** namnet Program.cs och lägg till följande kod i den för att anropa BasicSamples när projektet körs.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Nu bygga lösningen och tryck på F5 för att driva projektet. När projektet körs visas följande utdata i kommandotolken:

![Utdata från kommandotolken](./media/tutorial-develop-table-standard/output-from-sample.png)

Om du får ett felmeddelande om att filen Settings.json inte kan hittas när du kör projektet kan du lösa det genom att lägga till följande XML-post i projektinställningarna. Högerklicka på CosmosTableSamples, välj Redigera CosmosTableSamples.csproj och lägg till följande itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Nu kan du logga in på Azure-portalen och kontrollera att data finns i tabellen. 

![Resultat i portalen](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Nästa steg

Du kan nu gå vidare till nästa självstudie och lära dig hur du migrerar data till Azure Cosmos DB Table API-konto. 

> [!div class="nextstepaction"]
>[Så här frågar du data](../cosmos-db/table-import.md)
