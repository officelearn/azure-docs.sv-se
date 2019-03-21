---
title: Skapa en .NET-webbapp med Azure Cosmos DB med hjälp av SQL API
description: I den här snabbstarten använder du SQL-API för Azure Cosmos DB och Azure-portalen för att skapa en .NET-webbapp
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/15/2019
ms.openlocfilehash: 1ef414b2de2acbf5b92661c8b5f1e249549b14df
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259150"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-db-sql-api-account"></a>Snabbstart: Skapa en .NET-app med hjälp av Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> * [NET](create-sql-api-dotnet.md)
> * [.NET (förhandsversion)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här snabbstarten visar hur du skapar ett Azure Cosmos DB [SQL API](sql-api-introduction.md) kontot, dokumentera databas, samling och lägger till exempeldata till samlingen med hjälp av Azure portal. Du sedan skapa och distribuera en att göra-lista-webbapp som skapats med den [SQL .NET SDK](sql-api-sdk-dotnet.md), för att lägga till mer hantera data i samlingen. 

## <a name="prerequisites"></a>Förutsättningar

Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-account"></a>Skapa ett konto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection-database"></a>
## <a name="add-a-database-and-a-collection"></a>Lägg till en databas och en samling

Nu kan du använda datautforskarverktyget i Azure Portal för att skapa en databas och samling. 

1. Klicka på **Datautforskaren** > **Ny samling**. 
    
    Området **Lägg till samling** visas längst till höger, du kan behöva bläddra åt höger för att se det.

    ![Datautforskaren i Azure-portalen, fönstret Lägg till samling](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)

2. På sidan **Lägg till samling** anger du inställningarna för den nya samlingen.

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    **Databas-id**|ToDoList|Ange *ToDoList* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg.
    **Samlings-id**|Objekt|Ange *Objekt* som namnet på din nya samling. Samlings-ID har samma teckenkrav gäller som databasnamn.
    **Partitionsnyckel**| `<your_partition_key>`| Ange en partitionsnyckel. I exemplet som beskrivs i den här artikeln används */category* som partitionsnyckel.
    **Dataflöde**|400 RU|Ändra genomflödet till 400 begäransenheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden. 
    
    Utöver föregående inställningar kan du lägga till **unika nycklar** för samlingen om du vill. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. När du skapar en unik nyckelprincip medan du skapar en samling garanterar du unikheten för ett eller flera värden per partitionsnyckel. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](unique-keys.md).
    
    Klicka på **OK**.

    Datautforskaren visar den nya databasen och samlingen.

    ![Datautforskaren i Azure-portalen visar den nya databasen och samlingen](./media/create-sql-api-dotnet/azure-cosmos-db-new-collection.png)

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Lägg till exempeldata

Du kan nu lägga till data till din nya samling med datautforskaren.

1. Den nya databasen visas på panelen Samlingar i datautforskaren. Expandera databasen **Tasks** (Aktiviteter), expandera samlingen **Items** (Objekt), klicka på **Dokument** och klicka sedan på **Nya dokument**. 

   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
  
2. Lägg nu till ett dokument i samlingen med följande struktur.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. När du har lagt till json på fliken **Dokument** klickar du på **Spara**.

    ![Kopiera in json-data och klicka på Spara i Datautforskaren i Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)

4. Skapa och spara ännu ett dokument där du lägger till ett unikt värde för egenskapen `id` och ändrar de andra egenskaperna som passar. Dina nya dokument kan ha vilken struktur du vill eftersom Azure Cosmos DB inte kräver något schema för dina data.

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Vi ska klona en [SQL API-app från GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app), ställa in anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

4. Öppna därefter att göra-lösningsfilen i Visual Studio. 

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). I den här snabbstarten får du skapa en databas och en samling med hjälp av Azure-portalen och Lägg till exempeldata med hjälp av .NET-exempel. Men kan du också skapa databasen och samlingen med hjälp av .NET-exempel. 

Följande kodfragment är alla hämtade från filen DocumentDBRepository.cs.

* DocumentClient initieras som visas i följande kod:

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* En ny databas skapas med hjälp av den `CreateDatabaseAsync` metoden enligt följande kod:

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* En ny samling skapas med hjälp av den `CreateDocumentCollectionAsync` som visas i följande kod:

    ```csharp
    private static async Task CreateCollectionIfNotExistsAsync()
    {
        try
        {
           await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
        }
        catch (DocumentClientException e)
        {
           if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
           {
              await client.CreateDocumentCollectionAsync(
              UriFactory.CreateDatabaseUri(DatabaseId),
              new DocumentCollection
              {
                  Id = CollectionId
              },
              new RequestOptions { OfferThroughput = 400 });
           }
           else
           {
             throw;
           }
        }
    }
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen.

1. I [Azure-portalen](https://portal.azure.com/) går du till ditt Azure Cosmos DB-konto. Välj **Nycklar** i det vänstra navigeringsfönstret och sedan **läs- och skrivnycklar**. Du använder kopiera-knapparna till höger på skärmen för att kopiera URI:n och primärnyckeln i web.config-filen i nästa steg.

    ![Visa och kopiera åtkomstnyckeln i Azure Portal, bladet Nycklar](./media/create-sql-api-dotnet/keys.png)

2. I Visual Studio 2017 öppnar du web.config-filen. 

3. Kopiera ditt URI-värde från portalen (med kopieringsknappen) och gör det till värdet för slutpunktsnyckeln i web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Kopiera sedan värdet för primärnyckeln från portalen och gör det till värdet för authKey i web.config. 

    `<add key="authKey" value="FILLME" />`
    
5. Uppdatera sedan till databasen och samlingen värden som matchar namnet på databasen som du skapade tidigare. Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. 

   ```csharp
   <add key="database" value="ToDoList"/>
   <add key="collection" value="Items"/>
   ```
 
## <a name="run-the-web-app"></a>Kör webbappen
1. I Visual Studio högerklickar du på projektet i **Solution Explorer** och väljer därefter **Hantera NuGet-paket**. 

2. I NuGet-rutan **Bläddra**, skriver du in *DocumentDB*.

3. Från resultatet installerar du **Microsoft Azure DocumentDB**-biblioteket. Det här installerar Microsoft.Azure.DocumentDB-paketet samt alla beroenden.

4. Tryck på Ctrl + F5 för att köra programmet. Appen visas i din webbläsare. 

5. Välj **Skapa nytt** i webbläsaren och skapa några nya uppgifter i din att göra-app.

   ![Att göra-app med exempeldata](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

Du kan nu gå tillbaka till datautforskaren och se frågan, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur man skapar ett Azure Cosmos DB-konto, skapar en samling med datautforskaren och kör en webbapp. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)


