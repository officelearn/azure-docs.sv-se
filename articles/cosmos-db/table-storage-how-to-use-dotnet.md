---
title: Kom igång med Azure Table Storage och Table-API:et för Azure Cosmos DB med hjälp av .NET
description: Lagra strukturerade data i molnet med Azure Table Storage eller Table-API:et för Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 08/17/2018
ms.author: sngun
ms.openlocfilehash: fd437e42c404bec71036e760b95aef7e005d3fd9
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52880207"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-net"></a>Kom igång med Azure Table Storage och Table-API:et för Azure Cosmos DB med hjälp av .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Du kan använda Azure Table Storage eller Table-API:et för Azure Cosmos DB för att lagra strukturerade NoSQL-data i molnet, med ett nyckel-/attributlager med en schemalös design. Eftersom Table Storage och Table-API:et för Azure Cosmos DB är schemalösa kan du enkelt anpassa dina data baserat på hur ditt program utvecklas. Åtkomsten till data i Table Storage och Table-API:et för Azure Cosmos DB är snabb och kostnadseffektiv för många typer av program, och medför normalt lägre kostnad än traditionell SQL för liknande datavolymer.

Du kan använda Table Storage eller Table-API:et för Azure Cosmos DB för att lagra flexibla datauppsättningar som användardata för webbprogram, adressböcker, enhetsinformation eller andra typer av metadata som din tjänst behöver. Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto eller ett konto för Table-API:et kan innehålla valfritt antal tabeller, upp till kapacitetsgränsen för lagringskontot eller kontot för Table-API:et.

### <a name="about-this-sample"></a>Om det här exemplet
I det här exemplet visas hur du använder [Microsoft Azure CosmosDB-tabellbiblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) i vanliga scenarier för Azure Table Storage och Table-API:et. Paketets namn antyder att det ska användas med Azure Cosmos DB, men paketet fungerar med både Table-API:er för Azure Cosmos DB och Azure Table Storage, skillnaden är bara att tjänsterna har unika slutpunkter. Dessa scenarier analyseras med hjälp av C#-baserade exempel som beskriver hur du:
* Skapar och tar bort tabeller
* Infoga, uppdatera och ta bort rader
* Frågetabeller

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att kunna följa med i det här exemplet:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Gemensamt bibliotek för Azure Storage för .NET (förhandsversion)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). – Ett obligatoriskt förhandsversionspaket som stöds i produktionsmiljöer. 
* [Microsoft Azure CosmosDB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) – Det här biblioteket är för närvarande endast tillgängligt för .NET Standard. Det är inte tillgängligt för .NET Core än.
* [Azure Configuration Manager för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Azure Storage-konto](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Fler exempel
Ytterligare exempel med Table Storage finns i [Komma igång med Azure Table Storage i .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Du kan ladda ned exempelprogrammet och köra det eller bläddra i koden på GitHub.

## <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
* Det enklaste sättet att skapa ditt första Azure-lagringskonto är genom att använda [Azure Portal](https://portal.azure.com). Läs mer i [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

* Du kan också skapa ett Azure-lagringskonto med hjälp av [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md), eller [Lagringsresursprovider-klientbiblioteket för .NET](/dotnet/api/microsoft.azure.management.storage).

* Om du föredrar att inte skapa ett lagringskonto just nu så kan du också använda Azure-lagringsemulatorn för att köra och testa din kod i en lokal miljö. Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett konto för Table-API:et för Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

### <a name="create-a-windows-console-application-project"></a>Skapa ett Windows-konsolprogramprojekt
Skapa ett nytt Windows-konsolprogram i Visual Studio. Följande steg visar hur du skapar ett konsolprogram i Visual Studio 2017. Stegen är ungefär som i andra versioner av Visual Studio.

1. Välj **Arkiv** > **Nytt** > **Projekt**.
2. Välj **Installerat** > **Visual C#** > **Windows Classic Desktop**.
3. Välj **Konsolprogram (.NET Framework)**.
4. Ange ett namn för ditt program i fältet **Namn**.
5. Välj **OK**.

Alla kodexempel i det här exemplet kan läggas till i `Main()`-metoden i konsolprogrammets `Program.cs`-fil.

Du kan använda Azure CosmosDB-tabellbiblioteket i alla typer av .NET-program, t.ex. en Azure-molntjänst eller webbapp, eller i dator- och mobilprogram. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

### <a name="install-the-required-nuget-packages"></a>Installera de NuGet-paket som behövs
Det finns tre rekommenderade paket som du måste referera till i ditt projekt för att slutföra det här exemplet:

* [Gemensamt bibliotek för Azure Storage för .NET (förhandsversion)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common). – Använd en version som är mindre än eller lika med 9.0.0.1 (<= 9.0.0.1).

* [Microsoft Azure Cosmos DB-tabellbibliotek för .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Det här paketet ger programmatisk åtkomst till dataresurser i ditt Azure-tabellagringskonto eller konto för Table-API:et för Azure Cosmos DB. Det här biblioteket är för närvarande endast tillgängligt för .NET Standard. Det är inte tillgängligt för .NET Core än.

* [Microsoft Azure Configuration Manager-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): det här paketet tillhandahåller en klass för parsning av en anslutningssträng i en konfigurationsfil, oavsett var ditt program körs.

Du kan hämta NuGet-paketen genom att följa dessa steg:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.
2. Sök online efter ”Microsoft.Azure.Storage.Common”, välj en version som är <= 9.0.0.1 och välj **Installera** för att installera Azure Storage Common Library for .NET (förhandsversion) och dess beroenden. Kontrollera att rutan för att **ta med förhandsversionen** är markerad eftersom det här är ett förhandsversionspaket.
3. Sök online efter ”Microsoft.Azure.CosmosDB.Table” och välj **Installera** för att installera Microsoft Azure CosmosDB-tabellbiblioteket.
4. Sök online efter ”WindowsAzure.ConfigurationManager” och välj **Installera** för att installera Microsoft Azure Configuration Manager-biblioteket.

> [!NOTE]
> ODataLib-beroenden i det gemensamma biblioteket för Storage för .NET kan matchas med ODataLib-paketen som är tillgängliga på NuGet, inte från WCF Data Services. ODataLib-biblioteken kan hämtas direkt eller refereras till i ditt kodprojekt via NuGet. De specifika ODataLib-paket som används av Storage-klientbiblioteket är [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) och [Spatial](http://nuget.org/packages/System.Spatial/). Även om de här biblioteken används av Azure Table Storage-klasserna så är de nödvändiga beroenden för programmering med det gemensamma Storage-biblioteket.
> 
> 

> [!TIP]
> Utvecklare som redan är bekanta med Azure Table Storage kan ha använt paketet [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) tidigare. Vi rekommenderar att alla nya tabellprogram använder [gemensamma Azure Storage-bibliotek](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common) och [Azure Cosmos DB-tabellbiblioteket](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table), men paketet WindowsAzure.Storage går fortfarande att använda. Om du använder WindowsAzure.Storage-biblioteket ska du ta med Microsoft.WindowsAzure.Storage.Table i dina instruktioner.
>
>

### <a name="determine-your-target-environment"></a>Fastställ målmiljön
Du har tre miljöalternativ för att köra exemplen i den här guiden:

* Du kan köra din kod mot ett Azure Storage-konto i molnet. 
* Du kan köra din kod mot ett Azure Cosmos DB-konto i molnet.
* Du kan köra din kod mot en Azure-lagringsemulator. Lagringsemulatorn är en lokal miljö som emulerar ett Azure Storage-konto i molnet. Emulatorn är ett kostnadsfritt alternativ för att testa och felsöka din kod medan ditt program är under utveckling. Emulatorn använder sig av ett välkänt konto och nyckel. Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](../storage/common/storage-use-emulator.md).

Om målet är ett lagringskonto i molnet kopierar du den primära åtkomstnyckeln för ditt lagringskonto från Azure Portal. Mer information finns i [Åtkomstnycklar för lagringskonto](../storage/common/storage-account-manage.md#access-keys).

> [!NOTE]
> Du kan använda lagringsemulatorn för att undvika kostnader associerade med Azure Storage. Men även om du väljer att använda ett Azure-lagringskonto i molnet, kommer kostnaderna för att genomföra det här exemplet vara minimala.
> 
> 

Om målet är ett Azure Cosmos DB-konto kopierar du den primära åtkomstnyckeln för ditt konto för Table-API:et från Azure Portal. Mer information finns i [Uppdatera din anslutningssträng](create-table-dotnet.md#update-your-connection-string).

### <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
Det gemensamma Azure Storage-biblioteket för .NET stöder användning av en anslutningssträng för lagring för att konfigurera slutpunkter och autentiseringsuppgifter för åtkomst till lagringstjänster. Det bästa sättet att underhålla anslutningssträngen för lagring är i en konfigurationsfil. 

Mer information om anslutningssträngar finns i [Konfigurera en anslutningssträng för Azure Storage](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> Din kontonyckel liknar rotlösenordet för lagringskontot. Var alltid noga med att skydda din lagringskontonyckel. Undvik att dela ut den till andra användare, hårdkoda den eller spara den i en oformaterad textfil som andra har åtkomst till. Återskapa din nyckel med hjälp av Azure Portal om du misstänker att den komprometterats.
> 

För att konfigurera din anslutningssträng öppnar du `app.config`-filen i Solutions Explorer i Visual Studio. Lägg till innehållet i `<appSettings>`-elementet enligt nedan. Ersätt `account-name` med namnet på ditt konto och `account-key` med din åtkomstnyckel:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Om du exempelvis använder ett Azure Storage-konto liknar dina konfigurationsinställningar följande:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>" />
```

Om du använder ett Azure Cosmos DB-konto liknar dina konfigurationsinställningar följande:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=<account-key>;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

För att använda lagringsemulatorn kan du använda ett kortkommando som mappar till det välkända kontonamnet och nyckeln. I så fall är din inställning för anslutningssträngen:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Lägga till med hjälp av direktiv
Lägg till följande **using**-direktiv längst upp i filen `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Skapa tabelltjänstens klient
Med klassen [CloudTableClient][dotnet_CloudTableClient] kan du hämta tabeller och de entiteter som lagras i Table Storage. Här är ett exempel på hur du kan skapa Table Service-klienten:

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
Entiteter mappar till C#-objekt med hjälp av en anpassad klass som härleds från [TableEntity][dotnet_TableEntity]. Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. Följande kod definierar en entitetsklass som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel den unikt i tabellen. Det går snabbare att fråga entiteter med samma partitionsnyckel än entiteter som har olika partitionsnycklar, men skalbarheten och möjligheten att utföra parallella åtgärder är större med olika partitionsnycklar. Enheter som ska lagras i tabeller måste ha en typ som stöds, till exempel härledda från klassen [TableEntity] [dotnet_TableEntity]. Entitetsegenskaper som du vill lagra i en tabell måste vara offentliga egenskaper av den typen och ha stöd för att både hämta och ange värden. Dessutom *måste* entitetstypen exponera en parameterlös konstruktor.

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

Tabellåtgärder som rör entiteter utförs via [CloudTable][dotnet_CloudTable]-objektet som du skapade tidigare i avsnittet Skapa en tabell. Åtgärden som ska utföras representeras av ett [TableOperation][dotnet_TableOperation]-objekt. Följande kodexempel visar hur [CloudTable][dotnet_CloudTable]-objektet skapas och sedan ett **CustomerEntity**-objekt. För att förbereda åtgärden skapas ett [TableOperation][dotnet_TableOperation]-objekt som infogar kundentiteten i tabellen. Slutligen körs åtgärden genom att [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute] anropas.

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

Följande kod skapar två entitetsobjekt och lägger till vart och ett i [TableBatchOperation][dotnet_TableBatchOperation] med hjälp av [Insert][dotnet_TableBatchOperation_Insert]-metoden. Därefter anropas [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] för att köra åtgärden.

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
Om du vill fråga en tabell efter alla entiteter i en partition använder du ett [TableQuery][dotnet_TableQuery]-objekt. I följande kodexempel anges ett filter för entiteter där partitionsnyckeln är ”Smith”. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.

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
Du kan skriva en fråga för att hämta en enda, specifik entitet. I följande kod används [TableOperation][dotnet_TableOperation] för att ange kunden ”Ben Smith”. Den här metoden returnerar endast en entitet i stället för en samling, och värdet som returneras i [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] är ett **CustomerEntity**-objekt. Det snabbaste sättet att hämta en enskild entitet från tabelltjänsten är att ange både partitions- och radnycklar i en fråga.

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
Om du vill uppdatera en entitet hämtar du den från tabelltjänsten, ändrar entitetsobjektet och sparar sedan ändringarna till tabelltjänsten igen. Följande kod ändrar en befintlig kunds telefonnummer. I stället för att anropa [Insert][dotnet_TableOperation_Insert] använder den här koden [Replace][dotnet_TableOperation_Replace]. [Replace][dotnet_TableOperation_Replace] leder till att entiteten ersätts helt på servern, såvida inte entiteten på servern har ändrats sedan den hämtades. I så fall misslyckas åtgärden. Det här felet är avsett att förhindra att ditt program oavsiktligt skriver över ändringar mellan hämtningen och uppdateringen av en annan komponent i ditt program. Du hanterar det här felet genom att hämta entiteten igen, göra dina ändringar (om de fortfarande behövs) och sedan köra en [Replace][dotnet_TableOperation_Replace]-åtgärd igen. I nästa avsnitt visar vi hur du kan åsidosätta detta beteende.

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
[Replace][dotnet_TableOperation_Replace]-åtgärder misslyckas om entiteten har ändrats sedan den hämtades från servern. Dessutom måste du hämta entiteten från servern först för att [Replace][dotnet_TableOperation_Replace]-åtgärden ska lyckas. Ibland vet du dock inte om entiteten finns på servern, och vilka värden som för närvarande lagras i den är irrelevanta. Dina uppdateringar ska skriva över alla. För att åstadkomma detta använder du en [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]-åtgärd. Den här åtgärden infogar entiteten om den inte finns, eller ersätter den om den finns, oavsett när den senaste uppdateringen gjordes.

I följande kodexempel skapas en kundentitet för ”Fred Jones” och infogas i tabellen ”people”. Nu ska vi använda [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]-åtgärden för att spara en entitet med samma partitionsnyckel (Jones) och radnyckel (Fred) till servern, den här gången med ett annat värde för egenskapen PhoneNumber. Eftersom vi använder [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] ersätts alla dess egenskapsvärden. Men om det inte redan hade funnits en ”Fred Jones”-entitet i tabellen, skulle den ha infogats.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper
En tabellfråga kan hämta bara några få egenskaper från en entitet i stället för alla entitetsegenskaper. Den här tekniken, kallad projektion, minskar bandbredden och kan förbättra frågeprestanda, i synnerhet för stora entiteter. Frågan i följande kod returnerar bara e-postadresserna för entiteter i tabellen. Detta görs med hjälp av en fråga med [DynamicTableEntity][dotnet_DynamicTableEntity] och [EntityResolver][dotnet_EntityResolver]. Du kan lära dig mer om projektion i blogginlägget [Introducing Upsert and Query Projection][blog_post_upsert]. Projektion stöds inte av lagringsemulatorn, så den här koden körs bara när du använder ett konto i Table Service.

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
Du kan enkelt ta bort en enhet när du har hämtat den genom att använda samma mönster som när du uppdaterar en entitet. Följande kod hämtar och tar bort en kundentitet.

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

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* Du hittar fler Table Storage-exempel i [Komma igång med Azure Table Storage i .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* Fullständig information om tillgängliga API:er finns i referensdokumentationen för tabelltjänsten:
* [Storage-klientbibliotek för .NET-referens](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [REST API-referens](https://msdn.microsoft.com/library/azure/dd179355)
* Lär dig hur du förenklar koden du skriver så att den fungerar med Azure Storage genom att använda [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.
* [Kom igång med Azure Blob Storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) om du vill lagra ostrukturerade data.
* [Anslut till SQL Database med hjälp av .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) för att lagra relationsdata.

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
