---
title: Azure Cosmos DB Tabell-API med .NET standard SDK
description: Lär dig hur du lagrar och frågar strukturerade data i Azure Cosmos DB Tabell-API konto
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 80918c63c1fef82c13b23569bcda10545628e8e8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998024"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Kom igång med tabell-API för Azure Cosmos DB och Azure Table Storage med .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Du kan använda Azure Cosmos DB Tabell-API eller Azure Table Storage för att lagra strukturerade NoSQL-data i molnet, vilket ger ett Arkiv för nyckel/attribut med ett schema som är mindre design. Eftersom Azure Cosmos DB Tabell-API-och table-lagring är ett schema som är mindre, är det enkelt att anpassa dina data när dina program behöver utvecklas. Du kan använda Azure Cosmos DB Tabell-API eller tabell lagringen för att lagra flexibla data uppsättningar, till exempel användar data för webb program, adress böcker, enhets information eller andra typer av metadata som din tjänst kräver. 

I den här självstudien beskrivs ett exempel som visar hur du använder [Microsoft Azure Cosmos DB tabell bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) med Azure Cosmos DB tabell-API och Azure Table Storage-scenarier. Du måste använda den anslutning som är speciell för Azure-tjänsten. De här scenarierna är bekanta med C#-exempel som illustrerar hur du skapar tabeller, infogar/uppdaterar data, frågedata och tar bort tabeller.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna följa med i det här exemplet:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB-tabell bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) – det här biblioteket är för närvarande tillgängligt för .net standard och .NET Framework. 

* [Azure Cosmos DB tabell-API konto](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett Azure Cosmos DB Table API-konto

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Skapa ett .NET-konsol projekt

Skapa ett nytt .NET-konsol program i Visual Studio. Följande steg visar hur du skapar ett konsol program i Visual Studio 2019. Du kan använda Azure Cosmos DB tabell bibliotek i vilken typ av .NET-program som helst, inklusive en Azure-moln tjänst eller webbapp, samt Skriv bords-och mobil program. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

1. Välj **Arkiv**  >  **nytt**  >  **projekt**.

1. Välj **konsol program (.net Core)** och välj sedan **Nästa**.

1. I fältet **projekt namn** anger du ett namn för ditt program, till exempel **CosmosTableSamples**. (Du kan ange ett annat namn efter behov.)

1. Välj **Skapa**.

Alla kod exempel i det här exemplet kan läggas till i Main ()-metoden i konsol programmets **program.cs** -fil.

## <a name="install-the-required-nuget-package"></a>Installera det nödvändiga NuGet-paketet

Följ dessa steg för att hämta NuGet-paketet:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.

1. Sök online efter [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) , [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) och välj **Installera** för att installera Microsoft Azure Cosmos DB tabell bibliotek.

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

1. Från [Azure Portal](https://portal.azure.com/)navigerar du till ditt Azure Cosmos-konto eller Table Storage-kontot. 

1. Öppna fönstret **anslutnings sträng** eller **åtkomst nycklar** . Använd knapparna på höger sida av fönstret för att kopiera **PRIMÄR ANSLUTNINGSSTRÄNG**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Visa och kopiera PRIMÄR ANSLUTNINGSSTRÄNG i fönstret Anslutningssträng":::
   
1. Om du vill konfigurera anslutnings strängen från Visual Studio högerklickar du på projektet **CosmosTableSamples**.

1. Välj **Lägg till** och sedan **nytt objekt**. Skapa en ny fil **Settings.jspå** med filtypen **typescript JSON-konfigurationsfil** . 

1. Ersätt koden i Settings.jspå filen med följande kod och tilldela din primära anslutnings sträng:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Högerklicka på din Project- **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och Lägg till en klass med namnet **appSettings.cs**.

1. Lägg till följande kod i AppSettings.cs-filen. Den här filen läser anslutnings strängen från Settings.jspå filen och tilldelar den till konfigurations parametern:

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

## <a name="parse-and-validate-the-connection-details"></a>Analysera och verifiera anslutnings informationen 

1. Högerklicka på din Project- **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och Lägg till en klass med namnet **common.cs**. Du kommer att skriva kod för att verifiera anslutnings informationen och skapa en tabell i den här klassen.

1. Definiera en metod `CreateStorageAccountFromConnectionString` som visas nedan. Med den här metoden tolkas anslutnings Strängs informationen och du kan kontrol lera att konto namnet och konto nyckel informationen i filen "Settings.jspå" är giltig. 

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

Med klassen [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) kan du hämta tabeller och de entiteter som lagras i Table Storage. Eftersom vi inte har några tabeller i Cosmos DB Tabell-API-kontot ska vi lägga till `CreateTableAsync` metoden i klassen **common.cs** för att skapa en tabell:

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

Om du får fel meddelandet "503 Service Unavailable Exception" är det möjligt att de portar som krävs för anslutnings läget blockeras av en brand vägg. Du kan åtgärda det här problemet genom att antingen öppna de portar som krävs eller använda anslutnings tjänsten för gateway-läge som visas i följande kod:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Definiera entiteten 

Entiteter mappar till C#-objekt med hjälp av en anpassad klass som härletts från [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten.

Högerklicka på din Project- **CosmosTableSamples**. Välj **Lägg till**, **ny mapp** och ge den namnet som **modell**. I mappen modell lägger du till en klass med namnet **CustomerEntity.cs** och lägger till följande kod i den.

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

Den här koden definierar en entitets klass som använder kundens förnamn som rad nyckel och efter namn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel den unikt i tabellen. Entiteter med samma partitionsnyckel kan efter frågas snabbare än entiteter med olika partitionsnyckel, men med hjälp av olika partitionstyper kan du få bättre skalbarhet för parallella åtgärder. Enheter som ska lagras i tabeller måste ha en typ som stöds, till exempel härledda från klassen [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Entitetsegenskaper som du vill lagra i en tabell måste vara offentliga egenskaper av den typen och ha stöd för att både hämta och ange värden. Dessutom måste entitetstypen exponera en parameterlös konstruktor.

## <a name="insert-or-merge-an-entity"></a>Infoga eller sammanfoga en entitet

Följande kod exempel skapar ett entitet-objekt och lägger till det i tabellen. Metoden InsertOrMerge i klassen [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) används för att infoga eller slå samman en entitet. Metoden [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) anropas för att utföra åtgärden. 

Högerklicka på din Project- **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och Lägg till en klass med namnet **SamplesUtils.cs**. Den här klassen lagrar all kod som krävs för att utföra CRUD-åtgärder på entiteterna. 

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

## <a name="get-an-entity-from-a-partition"></a>Hämta en entitet från en partition

Du kan hämta entiteten från en partition med hjälp av metoden GET under klassen [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) . I följande kod exempel hämtas rad nyckel, e-postadress och telefonnummer för en kundentitet. I det här exemplet skrivs även de enheter för programbegäran som används för att fråga efter entiteten. Om du vill fråga efter en entitet lägger du till följande kod i **SamplesUtils.cs** -filen: 

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

Du kan enkelt ta bort en enhet när du har hämtat den genom att använda samma mönster som när du uppdaterar en entitet. Följande kod hämtar och tar bort en kundentitet. Om du vill ta bort en entitet lägger du till följande kod i **SamplesUtils.cs** -filen: 

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

## <a name="execute-the-crud-operations-on-sample-data"></a>Kör CRUD-åtgärder på exempel data

När du har definierat metoder för att skapa tabeller, infoga eller sammanfoga entiteter kör du dessa metoder i exempel data. Det gör du genom att högerklicka på projektet **CosmosTableSamples**. Välj **Lägg till**, **nytt objekt** och Lägg till en klass med namnet **BasicSamples.cs** och Lägg till följande kod till den. Den här koden skapar en tabell, lägger till entiteter i den. Om du vill ta bort entiteten och tabellen i slutet av projektet tar du bort kommentarerna från `table.DeleteIfExistsAsync()` och `SamplesUtils.DeleteEntityAsync(table, customer)` metoder från följande kod:

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

Föregående kod skapar en tabell som börjar med "demo" och det genererade GUID: t läggs till i tabell namnet. Sedan läggs en kundentitet med för-och efter namn till "Harp Walter" och senare uppdateras telefonnumret för den här användaren. 

I den här självstudien har du skapat kod för att utföra grundläggande CRUD-åtgärder på de data som lagras i Tabell-API-kontot. Du kan också utföra avancerade åtgärder, till exempel – batch infoga data, fråga alla data i en partition, fråga efter data intervall i en partition, listar tabeller i kontot vars namn börjar med det angivna prefixet. Du kan hämta det fullständiga exempel formuläret [Azure-Cosmos-Table-dotNet-Core-komma igång-GitHub-](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) lagringsplats. [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) -klassen har fler åtgärder som du kan utföra på data.  

## <a name="run-the-project"></a>Kör projektet

Från din Project- **CosmosTableSamples**. Öppna klassen med namnet **program.cs** och Lägg till följande kod i den för att anropa BasicSamples när projektet körs.

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

Nu ska du skapa lösningen och trycka på F5 för att köra projektet. När projektet körs visas följande utdata i kommando tolken:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Utdata från kommando tolken":::

Om du får ett fel meddelande om att det inte går att hitta Settings.jspå filen när du kör projektet kan du lösa det genom att lägga till följande XML-post i projekt inställningarna. Högerklicka på CosmosTableSamples, Välj Redigera CosmosTableSamples. CSPROJ och Lägg till följande itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Nu kan du logga in på Azure Portal och kontrol lera att data finns i tabellen. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Resultat i portalen":::

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta till nästa självstudie och lära dig hur du migrerar data till Azure Cosmos DB Tabell-API-kontot. 

> [!div class="nextstepaction"]
>[Fråga efter data](../cosmos-db/table-import.md)
