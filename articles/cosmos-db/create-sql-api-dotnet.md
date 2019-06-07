---
title: Skapa en .NET-webbapp med Azure Cosmos DB med hjälp av SQL API
description: I den här snabbstarten använder du Azure portal och en .NET-webbapp att skapa och hantera resurser i Azure Cosmos DB SQL API-kontot.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/06/2019
ms.openlocfilehash: e39440a46228d82b0722f7d9d349d11fb2417b42
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754664"
---
# <a name="quickstart-build-a-net-web-app-using-sql-api-account-in-azure-cosmos-db"></a>Snabbstart: Skapa en .NET-webbapp med SQL API-konto i Azure Cosmos DB

> [!div class="op_single_selector"]
> * [NET](create-sql-api-dotnet.md)
> * [.NET (förhandsversion)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga efter nyckel/värde-databaser, dokumentdatabaser och grafdatabaser fördelar av den globala distributionen och den horisontella skalningsförmågan i grunden hos Azure Cosmos DB. 

Den här snabbstarten visar hur du använder Azure-portalen för att skapa ett Azure Cosmos DB [SQL API](sql-api-introduction.md) konto, skapa en dokumentdatabas och samling och lägga till data i samlingen. Du sedan använda en [SQL .NET SDK](sql-api-sdk-dotnet.md) webbapp för att lägga till mer data i samlingen. 

I den här snabbstarten använder du Datautforskaren i Azure-portalen för att skapa databasen och samlingen. Du kan också skapa databas och samling med hjälp av .NET-exempelkod. Mer information finns i [granska .NET-kod](#review-the-net-code). 

## <a name="prerequisites"></a>Nödvändiga komponenter

Visual Studio 2019 med Azure-utveckling arbetsflödet installerad
- Du kan hämta och använda den **kostnadsfria** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio. 

En Azure-prenumeration eller ett kostnadsfritt utvärderingskonto för Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection-database"></a>
## <a name="add-a-database-and-a-collection"></a>Lägg till en databas och en samling 

Du kan använda Datautforskaren i Azure-portalen för att skapa en databas och samling. 

1.  Välj **Datautforskaren** i det vänstra navigeringsfönstret på Azure Cosmos DB-konto sida och välj sedan **ny behållare**. 
    
    Du kan behöva bläddra åt höger för att se den **Lägg till behållare** fönster.
    
    ![Datautforskaren i Azure-portalen, fönstret Lägg till samling](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  I den **Lägg till behållaren** fönstret anger du inställningarna för den nya samlingen.
    
    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|ToDoList|Ange *ToDoList* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrollera den **etablera databasen dataflöde** alternativ, kan du dela dataflödet som tillhandahållits till databasen över alla behållare i databasen. Det här alternativet hjälper också med kostnadsbesparingar. |
    |**Dataflöde**|400|Vill du lämna dataflödet på 400 begäransenheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Behållar-ID**|Objekt|Ange *Objekt* som namnet på din nya samling. Samlings-ID har samma teckenkrav gäller som databasnamn.|
    |**Partitionsnyckel**| /category| I exemplet som beskrivs i den här artikeln används */category* som partitionsnyckel.|

    
    Lägg inte till **unika nycklar** i det här exemplet. Unika nycklar kan du lägga till ett lager med dataintegritet till databasen genom att se till att ett eller flera värden per partitionsnyckel är unikt. Mer information finns i [unika nycklar i Azure Cosmos DB](unique-keys.md).
    
1.  Välj **OK**. Datautforskaren visar den nya databasen och den behållare som du skapade.
    

## <a name="add-data-to-your-database"></a>Lägg till data i databasen

Lägga till data i den nya databasen med hjälp av Datautforskaren.

1. I **Datautforskaren**, expandera den **ToDoList** databasen och expandera den **objekt** behållare. Välj sedan **objekt**, och välj sedan **nytt objekt**. 
   
   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Lägg till följande struktur till dokumentet på höger sida av den **dokument** fönstret:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Välj **Spara**.
   
   ![Kopiera in json-data och välj Spara i Datautforskaren i Azure portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Välj **nytt dokument** igen, och skapa och spara ett dokument med ett unikt `id`, och alla andra egenskaper och värden som du vill. Dokumenten kan ha vilken struktur, eftersom Azure Cosmos DB inte kräver något schema för dina data.

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="use-the-net-web-app-to-manage-data"></a>Använda .NET-webbapp för att hantera data

Om du vill se hur enkelt det är att arbeta med Azure Cosmos DB-data programmässigt, klona SQL API .NET webb-exempelappen från GitHub, uppdatera anslutningssträngen och köra appen om du vill uppdatera dina data. 

Du kan också skapa databasen och behållaren med hjälp av .NET-exempelkod. Mer information finns i [granska .NET-kod](#review-the-net-code).

### <a name="clone-the-sample-app"></a>Klona exempelappen

Först klonar en C# [SQL API-app](https://github.com/Azure-Samples/documentdb-dotnet-todo-app) från GitHub. 

1. Öppna ett git-terminalfönster, till exempel Git Bash, skapa en ny katalog med namnet *git-samples*, och ändra till den: 
   
   ```bash
   mkdir /c/git-samples/
   cd /c/git-samples/
   ```
   
1. Kör följande kommando för att klona exempellagringsplatsen och skapa en kopia av exempelappen på datorn:
   
   ```bash
   git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
   ```

### <a name="update-the-connection-string"></a>Uppdatera anslutningssträngen 

1. Navigera till och öppna den *todo.sln* filen för din klonade app i Visual Studio. 

1. I Visual Studio **Solution Explorer**öppnar den *web.config* fil. 

1. Gå tillbaka till Azure portal för att kopiera information om din anslutningssträng att klistra in i den *web.config*.
   
   1. I ditt Azure Cosmos DB-konto vänstra navigeringsfönstret väljer **nycklar**.
      
      ![Visa och kopiera åtkomstnyckeln i Azure Portal, bladet Nycklar](./media/create-sql-api-dotnet/keys.png)
      
   1. Under **Läs-och skrivnycklar**, kopiera den **URI** värdet med kopieringsknappen till höger och klistra in den i den `endpoint` nyckeln i den *web.config*. Exempel: 
      
      `<add key="endpoint" value="https://mysqlapicosmosdb.documents.azure.com:443/" />`
      
   1. Kopiera den **PRIMÄRNYCKEL** värde och klistra in den i den `authKey` nyckeln i den *web.config*. Exempel:
      
      `<add key="authKey" value="19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==" />`

       
1. Kontrollera att databasen och samlingen (kallas även för behållaren) värdena i den *web.config* matchar namnen som du skapade tidigare. 

   ```csharp
   <add key="database" value="ToDoList"/>
   <add key="collection" value="Items"/>
   ```
 
1. Spara den *web.config.* Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB.

### <a name="run-the-web-app"></a>Kör webbappen

1. I Visual Studio högerklickar du på den **todo** projekt i **Solution Explorer**, och välj sedan **hantera NuGet-paket**. 

1. I NuGet-rutan **Bläddra**, skriver du in *DocumentDB*.

1. Från resultatet installerar den **2.2.3 version** av **Microsoft.Azure.DocumentDB** biblioteket om du inte redan är installerat. Detta installerar de [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) paketet och alla beroenden.
   
   Om NuGet Package Manager visar ett meddelande om att vissa paket saknas i lösningen, Välj **återställa** att installera dem från interna källor. 

1. Välj **Ctrl**+**F5** att köra appen i webbläsaren. 

1. Välj **Skapa ny** i att göra-app och skapa några nya uppgifter.

   ![Att göra-app med exempeldata](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

Du kan gå tillbaka till Datautforskaren i Azure portal för att se, fråga, ändra och arbeta med dina nya data. 

## <a name="review-the-net-code"></a>Granska .NET-kod

Det här steget är valfritt. I den här snabbstarten du skapade en databas och en behållare i Azure portal och lagt exempeldata med hjälp av .NET-exempel. Men kan du också skapa databasen och behållaren med hjälp av .NET-exempel. Granska följande kodfragment om du vill veta hur databasresurserna skapas i koden. Alla kodavsnitten hämtas från den *documentdbrepository.CS så ser* fil i den **todo** projekt.

* Den här koden initierar den `DocumentClient`: 

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* Den här koden skapar den nya databasen med hjälp av den `CreateDatabaseAsync` metoden:

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* Följande kod skapar den nya samlingen med hjälp av den `CreateDocumentCollectionAsync` metoden:

    ```csharp
    private static async Task CreateCollectionIfNotExistsAsync(string partitionkey)
    {
       try
       {       
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), new RequestOptions { PartitionKey = new PartitionKey(partitionkey) });
       }
        catch (DocumentClientException e)
        {
           if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
            {
                await client.CreateDocumentCollectionAsync(
                  UriFactory.CreateDatabaseUri(DatabaseId),
                   new DocumentCollection
                    {
                      Id = CollectionId,
                      PartitionKey = new PartitionKeyDefinition
                       {
                           Paths = new System.Collections.ObjectModel.Collection<string>(new List<string>() { partitionkey })
                        }
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

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapa en databas och en behållare med Datautforskaren och kör en .NET-webbapp för att uppdatera dina data. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)

