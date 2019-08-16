---
title: Kom igång med Azure Table Storage och ASP.NET (Visual Studio Connected Services) | Microsoft Docs
description: Komma igång med Azure Table Storage i ett ASP.NET-projekt i Visual Studio efter anslutning till ett lagrings konto med hjälp av Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 6f0858d3c2e3f79dda58710031c105e83418058e
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511162"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Kom igång med Azure Table Storage och ASP.NET (Visual Studio Connected Services)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt

Med Azure Table Storage kan du lagra stora mängder strukturerade data. Tjänsten är ett NoSQL-datalager som accepterar autentiserade anrop inifrån och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.

Den här självstudien visar hur du skriver ASP.NET-kod för några vanliga scenarier med Azure Table Storage-entiteter. I dessa scenarier ingår att skapa en tabell och lägga till, fråga och ta bort tabell enheter. 

## <a name="prerequisites"></a>Förutsättningar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-konto](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Skapa en MVC-kontrollant 

1. I **Solution Explorer**högerklickar du på **kontrollanter**och väljer **Lägg till > kontrollant**på snabb menyn.

    ![Lägga till en kontrollant i en ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. I dialog rutan **Lägg till Autogenerera** väljer du **MVC 5 Controller-Töm**och väljer **Lägg till**.

    ![Ange typ av MVC-kontrollant](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. I dialog rutan **Lägg till kontrollant** namnger du kontrollantens *TablesController*och väljer **Lägg till**.

    ![Namnge MVC-kontrollanten](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Lägg till följande *med hjälp av* direktiv `TablesController.cs` i filen:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Skapa en modell klass

Många av exemplen i den här artikeln använder en **TableEntity**-härledd klass som heter **CustomerEntity**. Följande steg vägleder dig genom att deklarera den här klassen som en modell klass:

1. I **Solution Explorer**högerklickar du på **modeller**och väljer sedan **Lägg till > klass**från snabb menyn.

1. I dialog rutan **Lägg till nytt objekt** namnger du klassen **CustomerEntity**.

1. Öppna filen och Lägg till följande **med hjälp av** direktivet: `CustomerEntity.cs`

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Ändra klassen så att, när det är klart, är klassen deklarerad som i följande kod. Klassen deklarerar en entitets klass med namnet **CustomerEntity** som använder kundens förnamn som rad nyckel och efter namn som partitionsnyckel.

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
> I det här avsnittet förutsätter vi att du har slutfört stegen i [Konfigurera utvecklings miljön](#set-up-the-development-environment). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod med namnet **createTable** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **createTable** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient** -objekt representerar en tabell tjänst klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable** -objekt som representerar en referens till det önskade tabell namnet. Metoden **CloudTableClient. GetTableReference** gör ingen begäran mot Table Storage. Referensen returneras om tabellen finns eller inte. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Anropa metoden **CloudTable. CreateIfNotExists** för att skapa tabellen om den inte redan finns. Metoden **CloudTable. CreateIfNotExists** returnerar **True** om tabellen inte finns och har skapats. Annars returneras **false** .    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Uppdatera **ViewBag** med namnet på tabellen.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. I **Solution Explorer**, expanderar du mappen **vyer** , högerklickar på **tabeller**och väljer sedan **Lägg till > vy**från snabb menyn.

1. I dialog rutan **Lägg till vy** anger du **createTable** som namn på vyn och väljer **Lägg till**.

1. Öppna `CreateTable.cshtml`och ändra det så att det ser ut som i följande kodfragment:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Kör programmet och välj **Skapa tabell** för att se resultat som liknar följande skärm bild:
  
    ![Skapa tabell](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Som tidigare nämnts returnerar metoden **CloudTable. CreateIfNotExists** endast **True** när tabellen inte finns och skapas. Därför returnerar metoden **falskt**om du kör appen när tabellen finns. Om du vill köra appen flera gånger måste du ta bort tabellen innan du kör appen igen. Borttagning av tabellen kan göras via metoden **CloudTable. Delete** . Du kan också ta bort tabellen med hjälp av [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) eller [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

*Entiteter* mappar till\# C-objekt med hjälp av en anpassad klass som härletts från **TableEntity**. Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. I det här avsnittet får du se hur du definierar en entitets klass som använder kundens förnamn som rad nyckel och efter namn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Det går snabbare att fråga entiteter med samma partitionsnyckel än entiteter som har olika partitionsnycklar, men skalbarheten och möjligheten att utföra parallella åtgärder är större med olika partitionsnycklar. För alla egenskaper som ska lagras i tabell tjänsten måste egenskapen vara en offentlig egenskap för en typ som stöds och som visar både inställnings-och hämtnings värden.
Enhets klassen *måste* deklarera en offentlig parameter-mindre konstruktor.

> [!NOTE]
> 
> I det här avsnittet förutsätter vi att du har slutfört stegen i [Konfigurera utvecklings miljön](#set-up-the-development-environment).

1. Öppna filen `TablesController.cs`.

1. Lägg till följande direktiv så att koden i `TablesController.cs` filen kan komma åt klassen **CustomerEntity** :

    ```csharp
    using StorageAspnet.Models;
    ```

1. Lägg till en metod med namnet **AddEntity** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **AddEntity** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient** -objekt representerar en tabell tjänst klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable** -objekt som representerar en referens till den tabell som du kommer att lägga till den nya entiteten i. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instansiera och initiera klassen **CustomerEntity** .

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Skapa ett **TableOperation** -objekt som infogar entiteten kund.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Kör INSERT-åtgärden genom att anropa metoden **CloudTable. Execute** . Du kan kontrol lera resultatet av åtgärden genom att kontrol lera egenskapen **TableResult. HttpStatusCode** . Status koden 2xx anger att den åtgärd som begärdes av klienten har bearbetats. Till exempel, lyckade infogningar av nya entiteter resulterar i en HTTP-statuskod på 204, vilket innebär att åtgärden har bearbetats och att servern inte returnerade något innehåll.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Uppdatera **ViewBag** med tabell namnet och resultatet av INSERT-åtgärden.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. I **Solution Explorer**, expanderar du mappen **vyer** , högerklickar på **tabeller**och väljer sedan **Lägg till > vy**från snabb menyn.

1. I dialog rutan **Lägg till vy** anger du **AddEntity** som namn på vyn och väljer **Lägg till**.

1. Öppna `AddEntity.cshtml`och ändra det så att det ser ut som i följande kodfragment:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Kör programmet och välj **Lägg till entitet** för att se resultat som liknar följande skärm bild:
  
    ![Lägg till entitet](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Du kan kontrol lera att entiteten har lagts till genom att följa stegen i avsnittet och [Hämta en enda entitet](#get-a-single-entity). Du kan också använda [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att visa alla entiteter för dina tabeller.

## <a name="add-a-batch-of-entities-to-a-table"></a>Lägga till en batch med entiteter i en tabell

Förutom att kunna [lägga till en entitet i en tabell i taget](#add-an-entity-to-a-table), kan du också lägga till entiteter i batch. Om du lägger till entiteter i batch minskas antalet tur och inresor mellan koden och Azure Table service. Följande steg visar hur du lägger till flera entiteter i en tabell med en enda infognings åtgärd:

> [!NOTE]
> 
> I det här avsnittet förutsätter vi att du har slutfört stegen i [Konfigurera utvecklings miljön](#set-up-the-development-environment).

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod med namnet **AddEntities** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **AddEntities** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient** -objekt representerar en tabell tjänst klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable** -objekt som representerar en referens till den tabell som du kommer att lägga till de nya entiteterna i. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instansiera vissa kund objekt baserat på **CustomerEntity** modell klass som anges i avsnittet, [Lägg till en entitet i en tabell](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Hämta ett **TableBatchOperation** -objekt.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Lägg till entiteter i objektet batch infoga åtgärd.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Kör batch INSERT-åtgärden genom att anropa metoden **CloudTable. ExecuteBatch** .   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. Metoden **CloudTable. ExecuteBatch** returnerar en lista med **TableResult** -objekt där varje **TableResult** -objekt kan undersökas för att avgöra om varje enskild åtgärd lyckades eller inte. I det här exemplet skickar du listan till en vy och låter vyn Visa resultatet av varje åtgärd. 
 
    ```csharp
    return View(results);
    ```

1. I **Solution Explorer**, expanderar du mappen **vyer** , högerklickar på **tabeller**och väljer sedan **Lägg till > vy**från snabb menyn.

1. I dialog rutan **Lägg till vy** anger du **AddEntities** som namn på vyn och väljer **Lägg till**.

1. Öppna `AddEntities.cshtml`och ändra den så att den ser ut ungefär så här.

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

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Kör programmet och välj **Lägg till entiteter** för att se resultat som liknar följande skärm bild:
  
    ![Lägg till entiteter](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Du kan kontrol lera att entiteten har lagts till genom att följa stegen i avsnittet och [Hämta en enda entitet](#get-a-single-entity). Du kan också använda [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att visa alla entiteter för dina tabeller.

## <a name="get-a-single-entity"></a>Hämta en enskild entitet

I det här avsnittet beskrivs hur du hämtar en enskild entitet från en tabell med hjälp av enhetens rad nyckel och partitionsnyckel. 

> [!NOTE]
> 
> I det här avsnittet förutsätter vi att du har slutfört stegen i [Konfigurera utvecklings miljön](#set-up-the-development-environment)och använder data från [Lägg till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod med namnet **GetSingle** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **GetSingle** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient** -objekt representerar en tabell tjänst klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable** -objekt som representerar en referens till den tabell som du hämtar entiteten från. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa ett Hämta åtgärds objekt som tar ett enhetsobjektet härlett från **TableEntity**. Den första parametern är *partitionKey*och den andra parametern är *rowKey*. Om du använder klassen **CustomerEntity** och data som visas i avsnittet [Lägg till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table), så frågar följande kodfragment tabellen för en **CustomerEntity** -entitet med *partitionKey* -värdet "Smith" och a  *rowKey* värde för "ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Kör hämtnings åtgärden.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Skicka resultatet till vyn för visning.

    ```csharp
    return View(result);
    ```

1. I **Solution Explorer**, expanderar du mappen **vyer** , högerklickar på **tabeller**och väljer sedan **Lägg till > vy**från snabb menyn.

1. I dialog rutan **Lägg till vy** anger du **GetSingle** som namn på vyn och väljer **Lägg till**.

1. Öppna `GetSingle.cshtml`och ändra det så att det ser ut som i följande kodfragment:

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

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Kör programmet och välj **Hämta en** för att se resultat som liknar följande skärm bild:
  
    ![Hämta en enda](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition

Som anges i avsnittet [lägga till en entitet i en tabell](#add-an-entity-to-a-table), en kombination av en partition och en rad nyckel som unikt identifierar en entitet i en tabell. Entiteter med samma partitionsnyckel kan efter frågas snabbare än entiteter med olika partitionsnyckel. I det här avsnittet beskrivs hur du frågar en tabell efter alla entiteter från en angiven partition.  

> [!NOTE]
> 
> I det här avsnittet förutsätter vi att du har slutfört stegen i [Konfigurera utvecklings miljön](#set-up-the-development-environment)och använder data från [Lägg till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod med namnet **GetPartition** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **GetPartition** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient** -objekt representerar en tabell tjänst klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable** -objekt som representerar en referens till den tabell från vilken du hämtar entiteterna. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instansiera ett **TableQuery** -objekt som anger frågan i **WHERE** -satsen. Om du använder klassen **CustomerEntity** och data som visas i avsnittet [Lägg till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table), så frågar följande kodfragment tabellen för alla entiteter där **PartitionKey** (kundens efter namn) har värdet "Svensson":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. I en slinga anropar du metoden **CloudTable. ExecuteQuerySegmented** genom att skicka frågespråket som du instansierade i föregående steg.  Metoden **CloudTable. ExecuteQuerySegmented** returnerar ett **TableContinuationToken** -objekt som-när **Null** -indikerar att det inte finns några fler entiteter att hämta. I slingan använder du en annan slinga för att iterera över returnerade entiteter. I följande kod exempel läggs varje returnerad entitet till i en lista. När slingan är slut skickas listan till en vy för visning: 

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

1. I **Solution Explorer**, expanderar du mappen **vyer** , högerklickar på **tabeller**och väljer sedan **Lägg till > vy**från snabb menyn.

1. I dialog rutan **Lägg till vy** anger du **GetPartition** som namn på vyn och väljer **Lägg till**.

1. Öppna `GetPartition.cshtml`och ändra det så att det ser ut som i följande kodfragment:

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

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Kör programmet och välj **Hämta partition** för att se resultat som liknar följande skärm bild:
  
    ![Hämta partition](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Ta bort en entitet

I det här avsnittet beskrivs hur du tar bort en entitet från en tabell.

> [!NOTE]
> 
> I det här avsnittet förutsätter vi att du har slutfört stegen i [Konfigurera utvecklings miljön](#set-up-the-development-environment)och använder data från [Lägg till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod med namnet **DeleteEntity** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **DeleteEntity** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient** -objekt representerar en tabell tjänst klient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable** -objekt som representerar en referens till den tabell som du tar bort entiteten från. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa ett borttagnings åtgärds objekt som tar ett enhetsobjektet härlett från **TableEntity**. I det här fallet använder vi klassen **CustomerEntity** och data som visas i avsnittet [Lägg till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). Entitetens **etag** måste anges till ett giltigt värde.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Utför borttagnings åtgärden.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Skicka resultatet till vyn för visning.

    ```csharp
    return View(result);
    ```

1. I **Solution Explorer**, expanderar du mappen **vyer** , högerklickar på **tabeller**och väljer sedan **Lägg till > vy**från snabb menyn.

1. I dialog rutan **Lägg till vy** anger du **DeleteEntity** som namn på vyn och väljer **Lägg till**.

1. Öppna `DeleteEntity.cshtml`och ändra det så att det ser ut som i följande kodfragment:

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

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Kör programmet och välj **ta bort entitet** för att se resultat som liknar följande skärm bild:
  
    ![Hämta en enda](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Nästa steg
Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure Blob Storage och ASP.NET (Visual Studio Connected Services)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Kom igång med Azure Queue Storage och Visual Studio Connected Services (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
