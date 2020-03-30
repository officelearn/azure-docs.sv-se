---
title: Komma igång med Azure-tabelllagring med Visual Studio (ASP.NET)
description: Komma igång med Azure-tabelllagring i ett ASP.NET projekt i Visual Studio efter anslutning till ett lagringskonto med Visual Studio Connected Services
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c49df689ae859c93046c19af043aa2001dbb5481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979635"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Komma igång med Azure-tabelllagring och Visual Studio Connected Services (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Översikt

Med Azure Table Storage kan du lagra stora mängder strukturerad data. Tjänsten är ett NoSQL-datacenter som accepterar autentiserade samtal från och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data.

Den här självstudien visar hur du skriver ASP.NET kod för några vanliga scenarier med hjälp av Azure table storage entiteter. Dessa scenarier omfattar att skapa en tabell och lägga till, fråga och ta bort tabellentiteter. 

## <a name="prerequisites"></a>Krav

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure-lagringskonto](../storage/common/storage-account-create.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Skapa en MVC-styrenhet 

1. Högerklicka på Handkontroller i **Lösningsutforskaren**och välj **Lägg till >Controller**på snabbmenyn . **Controllers**

    ![Lägga till en styrenhet i en ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. I dialogrutan **Lägg till byggnadsställning** väljer du **MVC 5 Controller - Tom**och väljer Lägg **till**.

    ![Ange MVC-styrenhetstyp](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. I dialogrutan **Lägg till styrenhet** namnger du *controllertabellernakontrollera*och väljer **Lägg till**.

    ![Namnge MVC-styrenheten](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Lägg till följande med `TablesController.cs` hjälp *av* direktiv i filen:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Skapa en modellklass

Många av exemplen i den här artikeln använder en **TableEntity-härledd**klass som kallas **CustomerEntity**. Följande steg hjälper dig att deklarera den här klassen som en modellklass:

1. Högerklicka på **Modeller**i **Lösningsutforskaren**och välj **Lägg till >klass**på snabbmenyn .

1. I dialogrutan **Lägg till nytt objekt** namnger du klassen **CustomerEntity**.

1. Öppna `CustomerEntity.cs` filen och lägg till följande **med hjälp av** direktivet:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Ändra klassen så att klassen deklareras som i följande kod när den är klar. Klassen deklarerar en entitetsklass som kallas **CustomerEntity** som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel.

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
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **CreateTable** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **metoden CreateTable** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient-objekt** representerar en tabelltjänstklient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable-objekt** som representerar en referens till önskat tabellnamn. **Metoden CloudTableClient.GetTableReference** gör ingen begäran mot tabelllagring. Referensen returneras oavsett om tabellen finns eller inte. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Anropa metoden **CloudTable.CreateIfNotExists** för att skapa tabellen om den ännu inte finns. Metoden **CloudTable.CreateIfNotExists** returnerar **sant** om tabellen inte finns och har skapats. Annars returneras **falskt.**    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Uppdatera **ViewBag** med namnet på tabellen.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Tabeller**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du **CreateTable** för visningsnamnet och väljer **Lägg till**.

1. Öppna `CreateTable.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Kör programmet och välj **Skapa tabell** om du vill se resultat som liknar följande skärmbild:
  
    ![Skapa tabell](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Som tidigare nämnts returnerar metoden **CloudTable.CreateIfNotExists** **endast sant** när tabellen inte finns och skapas. Om du kör appen när tabellen finns returnerar metoden därför **false**. Om du vill köra appen flera gånger måste du ta bort tabellen innan du kör appen igen. Ta bort tabellen kan göras via **CloudTable.Delete-metoden.** Du kan också ta bort tabellen med [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040) eller [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

*Entiteter* \# mappar till C-objekt med hjälp av en anpassad klass som härleds från **TableEntity**. Om du vill lägga till en entitet i en tabell skapar du en klass som definierar egenskaperna för entiteten. I det här avsnittet får du se hur du definierar en entitetsklass som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Det går snabbare att fråga entiteter med samma partitionsnyckel än entiteter som har olika partitionsnycklar, men skalbarheten och möjligheten att utföra parallella åtgärder är större med olika partitionsnycklar. För alla egenskaper som ska lagras i tabelltjänsten måste egenskapen vara en offentlig egenskap av en typ som stöds och som exponerar både inställnings- och hämtningsvärden.
Entitetsklassen *måste* deklarera en offentlig parameter-utgående konstruktor.

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment).

1. Öppna filen `TablesController.cs`.

1. Lägg till följande direktiv så `TablesController.cs` att koden i filen kan komma åt klassen **CustomerEntity:**

    ```csharp
    using StorageAspnet.Models;
    ```

1. Lägg till en metod som kallas **AddEntity** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **metoden AddEntity** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient-objekt** representerar en tabelltjänstklient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable-objekt** som representerar en referens till den tabell som du ska lägga till den nya entiteten till. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instansiera och initiera **klassen CustomerEntity.**

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Skapa ett **TableOperation-objekt** som infogar kundentiteten.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Kör inåtgärden genom att anropa metoden **CloudTable.Execute.** Du kan verifiera resultatet av åtgärden genom att kontrollera egenskapen **TableResult.HttpStatusCode.** En statuskod på 2xx anger att den åtgärd som begärs av klienten har bearbetats. Till exempel resulterar lyckade infogningar av nya entiteter i en HTTP-statuskod 204, vilket innebär att åtgärden har bearbetats och att servern inte returnerade något innehåll.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Uppdatera **ViewBag** med tabellnamnet och resultatet av infogningen.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Tabeller**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du **AddEntity för visningsnamnet** och väljer Lägg **till**.

1. Öppna `AddEntity.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Kör programmet och välj **Lägg till entitet** om du vill se resultat som liknar följande skärmbild:
  
    ![Lägg till entitet](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Du kan kontrollera att entiteten har lagts till genom att följa stegen i avsnittet [Hämta en enda entitet](#get-a-single-entity). Du kan också använda [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att visa alla entiteter för dina tabeller.

## <a name="add-a-batch-of-entities-to-a-table"></a>Lägga till en grupp entiteter i en tabell

Förutom att kunna lägga till [en entitet i en tabell en i taget](#add-an-entity-to-a-table)kan du också lägga till entiteter i batch. Om du lägger till entiteter i batch minskar antalet tur-och-retur-resor mellan koden och Azure-tabelltjänsten. Följande steg illustrerar hur du lägger till flera entiteter i en tabell med en enda infogningsåtgärd:

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment).

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **Lägg till entiteter** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I metoden **Lägg till entiteter** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient-objekt** representerar en tabelltjänstklient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable-objekt** som representerar en referens till den tabell som du ska lägga till de nya entiteterna till. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instansiera vissa kundobjekt baserat på klassen **CustomerEntity-modell** som presenteras i avsnittet [Lägg till en entitet i en tabell](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Hämta ett **TableBatchOperation-objekt.**

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Lägg till entiteter i batchinfogningsåtgärdsobjektet.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Kör batchinfogningen genom att anropa metoden **CloudTable.ExecuteBatch.**   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. Metoden **CloudTable.ExecuteBatch** returnerar en lista över **TableResult-objekt** där varje **TableResult-objekt** kan undersökas för att fastställa framgång eller fel för varje enskild åtgärd. I det här exemplet skickar du listan till en vy och låter vyn visa resultatet av varje åtgärd. 
 
    ```csharp
    return View(results);
    ```

1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Tabeller**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du Lägg till **entiteter** för visningsnamnet och väljer **Lägg till**.

1. Öppna `AddEntities.cshtml`och ändra det så att det ser ut så här.

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

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Kör programmet och välj **Lägg till entiteter** för att se resultat som liknar följande skärmbild:
  
    ![Lägg till entiteter](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Du kan kontrollera att entiteten har lagts till genom att följa stegen i avsnittet [Hämta en enda entitet](#get-a-single-entity). Du kan också använda [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att visa alla entiteter för dina tabeller.

## <a name="get-a-single-entity"></a>Skaffa en enda entitet

Det här avsnittet illustrerar hur du hämtar en enda entitet från en tabell med hjälp av entitetens radnyckel och partitionsnyckel. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment)och använder data från [Lägg till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **GetSingle** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **Metoden GetSingle** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient-objekt** representerar en tabelltjänstklient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable-objekt** som representerar en referens till den tabell som du hämtar entiteten från. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa ett hämta åtgärdsobjekt som tar ett entitetsobjekt som härleds från **TableEntity**. Den första parametern är *partitionKey*, och den andra parametern är *rowKey*. Med hjälp av klassen **CustomerEntity** och data som visas i avsnittet [Lägg till en grupp entiteter i en tabell](#add-a-batch-of-entities-to-a-table)frågar följande kodavsnitt tabellen för en **CustomerEntity-entitetsentitet** med ett *partitionNyckelvärde* av "Smith" och ett *rowKey-värde* för "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Kör hämtningsåtgärden.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Skicka resultatet till vyn för visning.

    ```csharp
    return View(result);
    ```

1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Tabeller**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du **GetSingle** för visningsnamnet och väljer **Lägg till**.

1. Öppna `GetSingle.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

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

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Kör programmet och välj **Hämta singel** för att se resultat som liknar följande skärmbild:
  
    ![Få singel](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition

Som nämnts i avsnittet, [Lägg till en entitet i en tabell](#add-an-entity-to-a-table), identifiering en entitetskombination i en tabell. Entiteter med samma partitionsnyckel kan efterfrågas snabbare än entiteter med olika partitionsnycklar. Det här avsnittet visar hur du frågar en tabell för alla entiteter från en angiven partition.  

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment)och använder data från [Lägg till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **GetPartition** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **metoden GetPartition** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient-objekt** representerar en tabelltjänstklient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable-objekt** som representerar en referens till den tabell som du hämtar entiteterna från. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instansiera ett **TableQuery-objekt** som anger frågan i **where-satsen.** Med hjälp av klassen **CustomerEntity** och data som visas i avsnittet [Lägg till en grupp entiteter i en tabell](#add-a-batch-of-entities-to-a-table)frågar följande kodavsnitt tabellen för alla entiteter där **PartitionKey** (kundens efternamn) har värdet "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. I en loop anropar du metoden **CloudTable.ExecuteQuerySegmented** som skickar frågeobjektet som du instansierade i föregående steg.  **Metoden CloudTable.ExecuteQuerySegmented** returnerar ett **TableContinuationToken-objekt** som - när **null** - anger att det inte finns några fler entiteter att hämta. I loopen använder du en annan loop för att iterera över de returnerade entiteterna. I följande kodexempel läggs varje returnerad entitet till i en lista. När loopen är skickas listan till en vy för visning: 

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

1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Tabeller**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du **GetPartition** för visningsnamnet och väljer **Lägg till**.

1. Öppna `GetPartition.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

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

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Kör programmet och välj **Hämta partition** för att se resultat som liknar följande skärmbild:
  
    ![Hämta partition](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Ta bort en entitet

Det här avsnittet visar hur du tar bort en entitet från en tabell.

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [Konfigurera utvecklingsmiljön](#set-up-the-development-environment)och använder data från [Lägg till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). 

1. Öppna filen `TablesController.cs`.

1. Lägg till en metod som kallas **DeleteEntity** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I metoden **DeleteEntity** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudTableClient-objekt** representerar en tabelltjänstklient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Hämta ett **CloudTable-objekt** som representerar en referens till den tabell som du tar bort entiteten från. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Skapa ett borttagningsåtgärdsobjekt som tar ett entitetsobjekt som härleds från **TableEntity**. I det här fallet använder vi klassen **CustomerEntity** och data som presenteras i avsnittet [Lägg till en batch med entiteter i en tabell](#add-a-batch-of-entities-to-a-table). Entitetens **ETag** måste anges till ett giltigt värde.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Kör borttagningsåtgärden.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Skicka resultatet till vyn för visning.

    ```csharp
    return View(result);
    ```

1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Tabeller**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du **DeleteEntity för visningsnamnet** och väljer Lägg **till**.

1. Öppna `DeleteEntity.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

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

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Kör programmet och välj **Ta bort entiteten** om du vill visa resultat som liknar följande skärmbild:
  
    ![Få singel](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Nästa steg
Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Komma igång med Azure blob storage och Visual Studio Connected Services (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Komma igång med Azure-kölagring och Visual Studio Connected Services (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
