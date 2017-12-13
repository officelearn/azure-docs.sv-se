---
title: 'Azure Cosmos DB: skapa en webbapp med .NET och DocumentDB-API:n | Microsoft Docs'
description: "Presenterar ett .NET-kodexempel som du kan använda för att ansluta till och fråga Azure Cosmos DB DocumentDB-API:n"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: b8ab132a3e90032c4d70c310a2dd88f7441c4f0a
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="azure-cosmos-db-build-a-documentdb-api-web-app-with-net-and-the-azure-portal"></a>Azure Cosmos DB: skapa en DocumentDB-API-webbapp med .NET och Azure-portalen

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här snabbstarten demonstrerar hur man skapar ett Azure Cosmos DB-konto, en dokumentdatabas och en samling med hjälp av Azure-portalen. Därefter skapar och distribuerar du en webbapp med en att göra-lista som bygger på [DocumentDB .NET-API:n](documentdb-sdk-dotnet.md)som visas i följande skärmbild. 

![Att göra-app med exempeldata](./media/create-documentdb-dotnet/azure-comosdb-todo-app-list.png)

## <a name="prerequisites"></a>Krav

Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection"></a>
## <a name="add-a-collection"></a>Lägga till en samling

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Lägg till exempeldata

Du kan nu lägga till data till din nya samling med datautforskaren.

1. Den nya databasen visas på panelen Samlingar i datautforskaren. Expandera databasen **Tasks** (Aktiviteter), expandera samlingen **Items** (Objekt), klicka på **Dokument** och klicka sedan på **Nya dokument**. 

   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
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

    ![Kopiera in json-data och klicka på Spara i Datautforskaren i Azure Portal](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-save-document.png)

4.  Skapa och spara ännu ett dokument där du lägger till ett unikt värde för egenskapen `id` och ändrar de andra egenskaperna som passar. Dina nya dokument kan ha vilken struktur du vill eftersom Azure Cosmos DB inte kräver något schema för dina data.

     Du kan nu använda frågor i datautforskaren för att hämta dina data. Som standard används `SELECT * FROM c` i datautforskaren för att hämta alla dokument i samlingen, men du kan ändra det till en annan [SQL-fråga](documentdb-sql-query.md), till exempel `SELECT * FROM c ORDER BY c._ts DESC`, för att returnera alla dokument i fallande tidsstämpelordning.
 
     Du kan även använda datautforskaren för att skapa lagrade procedurer, UDF:er och utlösare för att utföra affärslogik på serversidan såväl som att skala genomflödet. Datautforskaren visar all den inbyggda programmässiga dataåtkomsten som finns tillgänglig i API:erna, men ger enkel åtkomst till dina data i Azure-portalen.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Nu ska vi klona en DocumentDB-API-app från GitHub, ange anslutningssträngen och köra den. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, till exempel git bash, och `CD` till en arbetskatalog.  

2. Klona exempellagringsplatsen med följande kommando. 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. Öppna därefter att göra-lösningsfilen i Visual Studio. 

## <a name="review-the-code"></a>Granska koden

Vi gör en snabb genomgång av vad som händer i appen. Öppna filen DocumentDBRepository.cs så ser du att de här kodraderna skapar Azure Cosmos DB-resurserna. 

* DocumentClient initieras på rad 78.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* En ny databas skapas på rad 93.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* En ny samling skapas på rad 112.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new DocumentCollection
            {
               Id = CollectionId,
               PartitionKey = new PartitionKeyDefinition() { Paths = new Collection<string>() { "/category" } }
            },
        new RequestOptions { OfferThroughput = 1000 });
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. I [Azure Portal](http://portal.azure.com/) går du till ditt Azure Cosmos DB-konto. Klicka på **Nycklar** i det västra navigeringsfönstret och sedan på **läs- och skrivnycklar**. Du använder kopiera-knapparna till höger på skärmen för att kopiera URI:n och primärnyckeln i web.config-filen i nästa steg.

    ![Visa och kopiera åtkomstnyckeln i Azure Portal, bladet Nycklar](./media/create-documentdb-dotnet/keys.png)

2. I Visual Studio 2017 öppnar du web.config-filen. 

3. Kopiera ditt URI-värde från portalen (med kopieringsknappen) och gör det till värdet för slutpunktsnyckeln i web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Kopiera sedan värdet för primärnyckeln från portalen och gör det till värdet för authKey i web.config. Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. 

    `<add key="authKey" value="FILLME" />`
    
## <a name="run-the-web-app"></a>Kör webbappen
1. I Visual Studio högerklickar du på projektet i **Solution Explorer** och därefter på **Hantera NuGet-paket**. 

2. I NuGet-rutan **Bläddra**, skriver du in *DocumentDB*.

3. Från resultatet installerar du **Microsoft Azure DocumentDB**-biblioteket. Det här installerar Microsoft.Azure.DocumentDB-paketet samt alla beroenden.

4. Tryck på Ctrl + F5 för att köra programmet. Appen visas i din webbläsare. 

5. Klicka på **Skapa nytt** i webbläsaren och skapa några nya uppgifter i din att göra-app.

   ![Att göra-app med exempeldata](./media/create-documentdb-dotnet/azure-comosdb-todo-app-list.png)

Du kan nu gå tillbaka till datautforskaren och se frågan, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen tar du bort alla resurser som skapades i snabbstarten i Azure Portal med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur man skapar ett Azure Cosmos DB-konto, skapar en samling med datautforskaren och kör en webbapp. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)


