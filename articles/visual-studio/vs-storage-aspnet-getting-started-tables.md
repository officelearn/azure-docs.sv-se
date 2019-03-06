---
title: Kom igång med Azure table storage och Visual Studio anslutna tjänster (ASP.NET) | Microsoft Docs
description: Hur du kommer igång med Azure table storage i ett ASP.NET-projekt i Visual Studio när du har anslutit till ett lagringskonto med hjälp av Visual Studio Connected Services
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 1a35d1cbd49a55b0c25f5e2b993358130589c125
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444641"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Kom igång med Azure table storage och Visual Studio anslutna tjänster (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt

Azure Table storage kan du lagra stora mängder strukturerade data. Tjänsten är en NoSQL-datalager som tar emot autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.

Den här självstudiekursen visar hur du skriver ASP.NET-kod för några vanliga scenarier med hjälp av Azure table storage-entiteter. Dessa scenarier är hur du skapar en tabell och lägger till, fråga och ta bort tabellenheter. 

## <a name="prerequisites"></a>Förutsättningar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-konto](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Skapa ett MVC-kontrollant 

1. I den **Solution Explorer**, högerklicka på **domänkontrollanter**, och välj sedan från snabbmenyn **Lägg till -> Controller**.

    ![Lägg till en domänkontrollant i en ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. På den **Lägg till Kodskelett** dialogrutan **MVC 5 styrenhet – tom**, och välj **Lägg till**.

    ![Ange typ av MVC-domänkontrollant](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. På den **Lägg till styrenhet** dialogrutan namn kontrollanten *TablesController*, och välj **Lägg till**.

    ![Namn på MVC-kontrollant](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Lägg till följande *med* direktiv den `TablesController.cs` fil:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Skapa en modellklass

Många av exemplen i den här artikeln används en **TableEntity**-härledd klass som kallas **CustomerEntity**. Följande steg hjälper dig att deklarera den här klassen som en modellklass:

1. I den **Solution Explorer**, högerklicka på **modeller**, och välj sedan från snabbmenyn **Lägg till -> klass**.

1. På den **Lägg till nytt objekt** dialogrutan namn klassen **CustomerEntity**.

1. Öppna den `CustomerEntity.cs` filen och Lägg till följande **med** direktiv:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Ändra klassen så att när du är klar klassen har deklarerats som i följande kod. Klassen deklarerar en entitetsklass som kallas **CustomerEntity** som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel.

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
    }
    ```

## <a name="create-a-table"></a>Skapa en tabell

Följande steg illustrerar hur du skapar en tabell:

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [ställer in utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **CreateTable** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **CreateTable** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en table service-klienten.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till önskad tabellens namn. Den **CloudTableClient.GetTableReference** inte gör en begäran mot tabellagring. Referensen returneras eller inte finns i tabellen. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Anropa den **CloudTable.CreateIfNotExists** metod för att skapa tabellen om det inte finns ännu. Den **CloudTable.CreateIfNotExists** metoden returnerar **SANT** om tabellen inte finns och har skapats. I annat fall **FALSKT** returneras.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Uppdatera den **ViewBag** med namnet på tabellen.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **CreateTable** för namn och välj **Lägg till**.

1. Öppna `CreateTable.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Kör programmet och välj **Create table** att se resultat som liknar följande skärmbild:
  
    ![Skapa tabell](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Som nämnts tidigare i **CloudTable.CreateIfNotExists** metoden returnerar **SANT** endast när tabellen finns inte och har skapats. Därför, om du kör appen när tabellen finns metoden returnerar **FALSKT**. För att köra appen flera gånger, måste du ta bort tabellen innan du kör appen igen. Tar bort tabellen kan ske via den **CloudTable.Delete** metod. Du kan också ta bort en tabell med hjälp av den [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040) eller [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

*Entiteter* mappas till C\# objekt med hjälp av en anpassad klass som härleds från **TableEntity**. Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. I det här avsnittet visas hur du definierar en entitetsklass som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Det går snabbare att fråga entiteter med samma partitionsnyckel än entiteter som har olika partitionsnycklar, men skalbarheten och möjligheten att utföra parallella åtgärder är större med olika partitionsnycklar. För egenskaper som ska lagras i table service, måste egenskapen vara en offentlig egenskap för en typ som stöds som exponerar både inställnings- och hämtningsvärden.
Enhetsklassen *måste* deklarera en offentlig parameterlös konstruktor.

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [ställer in utvecklingsmiljön](#set-up-the-development-environment).

1. Öppna filen `TablesController.cs`.

1. Lägg till följande direktiv så att koden i den `TablesController.cs` filen kan komma åt den **CustomerEntity** klass:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Lägg till en metod som kallas **AddEntity** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **AddEntity** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en table service-klienten.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till tabellen som du ska lägga till den nya entiteten. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa en instans av och initiera den **CustomerEntity** klass.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Skapa en **TableOperation** objekt som infogar kundentiteten.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Köra insert-åtgärden genom att anropa den **CloudTable.Execute** metod. Du kan kontrollera resultatet av åtgärden genom att granska den **TableResult.HttpStatusCode** egenskapen. Statuskoden 2xx anger den åtgärd som begärs av klienten har bearbetats. Lyckad infogningar av nya entiteter resulterar i en HTTP-statuskod 204, vilket innebär att åtgärden har bearbetats och servern returnerade exempelvis inte något innehåll.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Uppdatera den **ViewBag** med tabellnamnet på och resultatet av insert-åtgärden.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **AddEntity** för namn och välj **Lägg till**.

1. Öppna `AddEntity.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Kör programmet och välj **Lägg till entitet** att se resultat som liknar följande skärmbild:
  
    ![Lägg till entitet](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Du kan kontrollera att entiteten har lagts till genom att följa stegen i avsnittet [hämta en enda entitet](#get-a-single-entity). Du kan också använda den [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md) att visa alla entiteter för dina tabeller.

## <a name="add-a-batch-of-entities-to-a-table"></a>Lägg till en batch med entiteter i en tabell

Förutom att [lägga till en entitet i en tabell en i taget](#add-an-entity-to-a-table), du kan också lägga till entiteter i batch. Lägger till entiteter i batch minskar antalet turer mellan din kod och Azure table service. Följande steg illustrerar hur du lägger till flera entiteter i en tabell med en enda Infoga åtgärd:

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [ställer in utvecklingsmiljön](#set-up-the-development-environment).

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **AddEntities** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **AddEntities** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en table service-klienten.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till tabellen som du ska lägga till nya entiteter. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa en instans av vissa kundobjekt baserat på den **CustomerEntity** modellklass som visas i avsnittet [lägga till en entitet i en tabell](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Hämta en **TableBatchOperation** objekt.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Lägg till entiteter i batch Infoga åtgärden objekt.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Köra batch insert-åtgärden genom att anropa den **CloudTable.ExecuteBatch** metod.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. Den **CloudTable.ExecuteBatch** metoden returnerar en lista över **TableResult** objekt där varje **TableResult** objekt kan undersökas för att fastställa lyckats eller misslyckats varje enskild åtgärd. I det här exemplet skickar listan till en vy och låt vyn Visa resultatet av varje åtgärd. 
 
    ```csharp
    return View(results);
    ```

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **AddEntities** för namn och välj **Lägg till**.

1. Öppna `AddEntities.cshtml`, och ändra det så att det ser ut som följande.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Kör programmet och välj **Lägg till entiteter** att se resultat som liknar följande skärmbild:
  
    ![Lägg till entiteter](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Du kan kontrollera att entiteten har lagts till genom att följa stegen i avsnittet [hämta en enda entitet](#get-a-single-entity). Du kan också använda den [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md) att visa alla entiteter för dina tabeller.

## <a name="get-a-single-entity"></a>Hämta en enda entitet

Det här avsnittet visar hur du hämtar en enskild entitet från en tabell med de entitets radnyckel och partitionsnyckel. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [ställer in utvecklingsmiljön](#set-up-the-development-environment), och använder data från [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **GetSingle** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **GetSingle** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en table service-klienten.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till tabellen som du hämtar entiteten. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa ett objekt för hämta åtgärd som tar ett entitetsobjekt som härleds från **TableEntity**. Den första parametern är den *partitionKey*, och den andra parametern är den *rowKey*. Med hjälp av den **CustomerEntity** klass- och visas i avsnittet [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table), följande kodfragment frågar tabellen för en **CustomerEntity**entitet med en *partitionKey* värdet för ”Smith” och en *rowKey* värdet för ”Ben”:

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Hämta åtgärd för körning.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Skicka resultatet till vyn för visning.

    ```csharp
    return View(result);
    ```

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **GetSingle** för namn och välj **Lägg till**.

1. Öppna `GetSingle.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Kör programmet och välj **få enkel** att se resultat som liknar följande skärmbild:
  
    ![Få enkel](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition

Som vi redan nämnt i avsnittet [lägga till en entitet i en tabell](#add-an-entity-to-a-table), kombinationen av en partition och en radnyckel unikt identifiera en entitet i en tabell. Entiteter med samma partitionsnyckel kan frågas snabbare än entiteter med olika partitionsnycklar. Det här avsnittet visar hur du kan fråga en tabell efter alla entiteter från en angiven partition.  

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [ställer in utvecklingsmiljön](#set-up-the-development-environment), och använder data från [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **GetPartition** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **GetPartition** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en table service-klienten.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till tabellen som du hämtar entiteter. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa en instans av en **TableQuery** objekt som anger frågan i den **där** satsen. Med hjälp av den **CustomerEntity** klass- och visas i avsnittet [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table), följande kodfragment frågar tabellen för alla entiteter där den  **PartitionKey** (kundens efternamn) har värdet ”Smith”:

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. I en loop anropa den **CloudTable.ExecuteQuerySegmented** metoden skicka frågeobjektet du instansierats i föregående steg.  Den **CloudTable.ExecuteQuerySegmented** metoden returnerar en **TableContinuationToken** objekt som, när **null** -indikerar att det finns inga fler entiteter att hämta. Använda en annan loop inom loopen, iterera över returnerade entiteter. I följande kodexempel läggs varje returnerade entitet till i en lista. När loopen avslutas i listan har överförts till en vy för att visa: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **GetPartition** för namn och välj **Lägg till**.

1. Öppna `GetPartition.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Kör programmet och välj **hämta Partition** att se resultat som liknar följande skärmbild:
  
    ![Hämta Partition](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Ta bort en entitet

Det här avsnittet visas hur du tar bort en entitet från en tabell.

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [ställer in utvecklingsmiljön](#set-up-the-development-environment), och använder data från [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **DeleteEntity** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **DeleteEntity** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en table service-klienten.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till tabellen från vilken du vill ta bort entiteten. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa ett objekt för ta bort åtgärden som tar ett entitetsobjekt som härleds från **TableEntity**. I det här fallet använder vi den **CustomerEntity** klass- och visas i avsnittet [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). Entitetens **ETag** måste anges till ett giltigt värde.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Kör åtgärden ta bort.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Skicka resultatet till vyn för visning.

    ```csharp
    return View(result);
    ```

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **DeleteEntity** för namn och välj **Lägg till**.

1. Öppna `DeleteEntity.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Kör programmet och välj **ta bort entitet** att se resultat som liknar följande skärmbild:
  
    ![Få enkel](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Nästa steg
Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure blob storage och Visual Studio anslutna tjänster (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Kom igång med Azure queue storage och Visual Studio anslutna tjänster (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
