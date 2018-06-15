---
title: Kom igång med Azure-tabellagring och Visual Studio anslutna tjänster (ASP.NET) | Microsoft Docs
description: Hur du kommer igång med Azure-tabellagring i ASP.NET-projekt i Visual Studio efter anslutning till ett lagringskonto med hjälp av Visual Studio anslutna Services
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 39456380769e1c3b790d2bbc6fdf9c04c983d054
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798639"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Kom igång med Azure-tabellagring och Visual Studio anslutna tjänster (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt

Azure Table storage kan du lagra stora mängder strukturerade data. Tjänsten är en NoSQL-datalager som tar emot autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.

Den här kursen visar hur du skriver ASP.NET-kod för några vanliga scenarier med hjälp av Azure table storage entiteter. Dessa scenarier som inkluderar att skapa en tabell och lägga till, fråga och ta bort tabellentiteter. 

## <a name="prerequisites"></a>Förutsättningar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-konto](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Skapa en MVC-enhet 

1. I den **Solution Explorer**, högerklicka på **domänkontrollanter**, och på snabbmenyn Välj **Lägg till -> styrenhet**.

    ![Lägg till en domänkontrollant i en ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. På den **Lägg till Kodskelett** markerar **MVC 5 styrenhet – tom**, och välj **Lägg till**.

    ![Ange typ av MVC-domänkontrollant](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. På den **Lägg till styrenhet** dialogrutan namn styrenheten *TablesController*, och välj **Lägg till**.

    ![Namnet på MVC-enhet](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Lägg till följande *med* direktiven till den `TablesController.cs` filen:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Skapa en modellklass

Många av exemplen i den här artikeln används en **TableEntity**-härledd klass som kallas **CustomerEntity**. Följande steg när du går igenom deklarera den här klassen som en modellklass:

1. I den **Solution Explorer**, högerklicka på **modeller**, och på snabbmenyn Välj **klassen -> Lägg till**.

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

Följande steg visar hur du skapar en tabell:

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **CreateTable** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **CreateTable** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en tabelltjänstens klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till önskad tabellens namn. Den **CloudTableClient.GetTableReference** inte gör en begäran mot tabellagring. Referensen returneras eller inte finns i tabellen. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Anropa den **CloudTable.CreateIfNotExists** metod för att skapa tabellen om det inte finns ännu. Den **CloudTable.CreateIfNotExists** metoden returnerar **SANT** om tabellen inte finns och har skapats. Annars **FALSKT** returneras.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Uppdatering av **ViewBag** med namnet på tabellen.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **CreateTable** för namn och välj **Lägg till**.

1. Öppna `CreateTable.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Kör programmet och välj **Skapa tabell** att se resultatet liknar följande Skärmdump:
  
    ![Skapa tabell](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Som nämnts tidigare i **CloudTable.CreateIfNotExists** metoden returnerar **SANT** endast när tabellen finns inte och har skapats. Om du kör appen när tabellen finns metoden returnerar därför **FALSKT**. Om du vill köra appen flera gånger, måste du ta bort tabellen innan du kör appen igen. Ta bort tabellen kan göras den **CloudTable.Delete** metod. Du kan också ta bort en tabell med hjälp av den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040) eller [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

*Entiteter* mappas till C\# objekt med hjälp av en anpassad klass som härleds från **TableEntity**. Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. I det här avsnittet visas hur du definierar en entitetsklass som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Det går snabbare att fråga entiteter med samma partitionsnyckel än entiteter som har olika partitionsnycklar, men skalbarheten och möjligheten att utföra parallella åtgärder är större med olika partitionsnycklar. Egenskapen måste vara en offentlig egenskap för en typ som stöds som exponerar både inställningen och hämtar värden för alla egenskaper som ska lagras i tabelltjänsten.
Enhetsklassen *måste* deklarera en offentlig parameterlös konstruktor.

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment).

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

1. I den **AddEntity** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en tabelltjänstens klient.
   
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

1. Köra insert-åtgärden genom att anropa den **CloudTable.Execute** metod. Du kan kontrollera resultatet av åtgärden genom att kontrollera den **TableResult.HttpStatusCode** egenskapen. Statuskoden 2xx anger den åtgärd som begärs av klienten har bearbetats. Lyckad infogningar av nya enheter resulterar i en HTTP-statuskod 204, vilket innebär att åtgärden bearbetades och servern returnerade exempelvis inte allt innehåll.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Uppdatering av **ViewBag** med tabellnamnet och resultatet av insert-åtgärden.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **AddEntity** för namn och välj **Lägg till**.

1. Öppna `AddEntity.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Kör programmet och välj **lägga till enheten** att se resultatet liknar följande Skärmdump:
  
    ![Lägga till entitet](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Du kan kontrollera att entiteten har lagts till genom att följa stegen i avsnittet [hämta en enda entitet](#get-a-single-entity). Du kan också använda den [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md) att visa alla entiteter för tabeller.

## <a name="add-a-batch-of-entities-to-a-table"></a>Lägg till en batch med entiteter i en tabell

Förutom att kunna [lägga till en entitet i en tabell en i taget](#add-an-entity-to-a-table), du kan också lägga till entiteter i en batch. Lägger till enheter i batch minskar antalet turer mellan din kod och tjänsten Azure-tabellen. Följande steg visar hur du lägger till flera enheter till en tabell med en enda infoga igen:

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment).

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **AddEntities** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **AddEntities** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en tabelltjänstens klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till tabellen som du ska lägga till nya enheter. 
   
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

1. Lägga till enheter i objektet batch insert-åtgärden.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Köra batch insert-åtgärden genom att anropa den **CloudTable.ExecuteBatch** metod.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. Den **CloudTable.ExecuteBatch** metoden returnerar en lista över **TableResult** objekt där varje **TableResult** objekt kan undersökas för att fastställa lyckad eller misslyckad för varje enskild transaktion. I det här exemplet skickar listan till en vy och låt den vy som visar resultatet av varje åtgärd. 
 
    ```csharp
    return View(results);
    ```

1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **AddEntities** för namn och välj **Lägg till**.

1. Öppna `AddEntities.cshtml`, och ändra den så att det ser ut ungefär så här.

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

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Kör programmet och välj **lägga till enheter** att se resultatet liknar följande Skärmdump:
  
    ![Lägg till entiteter](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Du kan kontrollera att entiteten har lagts till genom att följa stegen i avsnittet [hämta en enda entitet](#get-a-single-entity). Du kan också använda den [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md) att visa alla entiteter för tabeller.

## <a name="get-a-single-entity"></a>Hämta en enda entitet

Det här avsnittet beskriver hur du får en enda entitet från en tabell med hjälp av den entitets radnyckel och partitionsnyckel. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment), och använder data från [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **GetSingle** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **GetSingle** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en tabelltjänstens klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till tabellen från vilken du hämtar entiteten. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa ett objekt för hämta åtgärd som tar ett entitetsobjekt som härletts från **TableEntity**. Den första parametern är den *partitionKey*, och den andra parametern är den *rowKey*. Med hjälp av den **CustomerEntity** klass och data som anges i avsnittet [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table), följande kodavsnitt frågar tabellen för en **CustomerEntity** entitet med en *partitionKey* värdet för ”Smith” och en *rowKey* värdet för ”Ben”:

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Köra hämtningen.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Skicka resultatet till vyn för visning.

    ```csharp
    return View(result);
    ```

1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **GetSingle** för namn och välj **Lägg till**.

1. Öppna `GetSingle.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

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

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Kör programmet och välj **hämta enda** att se resultatet liknar följande Skärmdump:
  
    ![Hämta enda](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition

Som nämnts i avsnittet [lägga till en entitet i en tabell](#add-an-entity-to-a-table), identifiera kombinationen av en partition och en rad för en entitet i en tabell. Entiteter med samma partitionsnyckel kan frågas snabbare än entiteter med olika partitionsnycklar. Det här avsnittet visar hur du kan fråga en tabell efter alla entiteter från en angiven partition.  

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment), och använder data från [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **GetPartition** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **GetPartition** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en tabelltjänstens klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till tabellen från vilken du hämtar entiteterna. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa en instans av en **TableQuery** objekt som anger frågan i den **där** satsen. Med hjälp av den **CustomerEntity** klass och data som anges i avsnittet [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table), följande kodavsnitt frågar tabellen för alla enheter där den **PartitionKey** (kundens efternamn) har värdet ”Smith”:

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. I en slinga anropa den **CloudTable.ExecuteQuerySegmented** metoden skickar frågeobjektet du instansierad i föregående steg.  Den **CloudTable.ExecuteQuerySegmented** metoden returnerar en **TableContinuationToken** objekt som - när **null** -anger att det inte finns några fler enheter att hämta. Använd en annan loop inom loopen, och iterera över returnerade entiteter. I följande kodexempel läggs varje returnerade entitet till en lista. När loopen avslutas listan skickas till en vy för visning: 

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

1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **GetPartition** för namn och välj **Lägg till**.

1. Öppna `GetPartition.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

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

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Kör programmet och välj **hämta partitionen** att se resultatet liknar följande Skärmdump:
  
    ![Hämta Partition](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Ta bort en entitet

Det här avsnittet beskriver hur du tar bort en entitet från en tabell.

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment), och använder data från [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **DeleteEntity** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **DeleteEntity** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudTableClient** -objektet representerar en tabelltjänstens klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta en **CloudTable** objekt som representerar en referens till tabellen från vilken du vill ta bort entiteten. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa åtgärden ta bort objekt som tar ett entitetsobjekt som härletts från **TableEntity**. I det här fallet används den **CustomerEntity** klass och data som anges i avsnittet [lägga till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). Entitetens **ETag** måste anges till ett giltigt värde.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Köra delete-åtgärden.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Skicka resultatet till vyn för visning.

    ```csharp
    return View(result);
    ```

1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **tabeller**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **DeleteEntity** för namn och välj **Lägg till**.

1. Öppna `DeleteEntity.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

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

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Kör programmet och välj **ta bort entiteten** att se resultatet liknar följande Skärmdump:
  
    ![Hämta enda](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Nästa steg
Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure blob storage och Visual Studio anslutna tjänster (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Kom igång med Azure queue storage- och Visual Studio anslutna tjänster (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
