---
title: Skapa en .NET-app i Azure Cosmos DB med tabell-API:t | Microsoft Docs
description: "Kom igång med tabell-API:t i Azure Cosmos DB med hjälp av .NET"
services: cosmosdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: cba0b278d84e25876a8b73cedb7e35f84500fc5e
ms.contentlocale: sv-se
ms.lasthandoff: 05/11/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: Skapa en .NET-app med tabell-API:t

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här snabbstarten demonstrerar hur du skapar ett Azure Cosmos DB-konto och en tabell i kontot med hjälp av Azure Portal. Sedan kommer du att skriva kod för att infoga, uppdatera och ta bort entiteter samt köra några frågor. Du kan hämta [förhandsversionen av Azure Storage SDK](https://aka.ms/premiumtablenuget) från NuGet. Den har samma klasser och metodsignaturer som den offentliga versionen av [Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), men även funktioner för att ansluta till Azure Cosmos DB-konton via [tabell-API:t](table-introduction.md) (förhandsversion). 

## <a name="prerequisites"></a>Krav

Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmosdb-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Lägg till en tabell

[!INCLUDE [cosmosdb-create-table](../../includes/cosmosdb-create-table.md)]

## <a name="add-sample-data"></a>Lägg till exempeldata

Du kan nu lägga till data till den nya tabellen med datautforskaren.

1. Öppna Datautforskaren, expandera **sample-database** och **sample-table**, klicka på **Entiteter** och sedan på **Lägg till entitet**.
2. Lägg till data i värderutorna för egenskaperna PartitionKey (Partitionsnyckel) och RowKey (Radnyckel) och klicka på **Lägg till entitet**.

   ![Skapa nya dokument i datautforskaren i Azure Portal](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
    Du kan nu lägga till fler entiteter i din tabell, redigera entiteter eller ställa frågor mot data i Datautforskaren. I Datautforskaren kan du även skala ditt dataflöde och lägga till lagrade procedurer, användardefinierade funktioner och utlösare i tabellen.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en DocumentDB-API-app från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, till exempel git bash, och `cd` till en arbetskatalog.  

2. Klona exempellagringsplatsen med följande kommando. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Öppna därefter lösningsfilen i Visual Studio. 

## <a name="review-the-code"></a>Granska koden

Vi gör en snabb genomgång av vad som händer i appen. Öppna filen Program.cs så ser du att de här kodraderna skapar Azure Cosmos DB-resurserna. 

* DocumentClient initieras.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* En ny tabell skapas om det inte finns någon.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* En ny tabellbehållare skapas. Du märker att den här koden är mycket lik vanliga SDK:er för Azure Table Storage 

    ```csharp
    CustomerEntity item = new CustomerEntity()
                {
                    PartitionKey = Guid.NewGuid().ToString(),
                    RowKey = Guid.NewGuid().ToString(),
                    Email = $"{GetRandomString(6)}@contoso.com",
                    PhoneNumber = "425-555-0102",
                    Bio = GetRandomString(1000)
                };
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. Öppna ditt Azure Cosmos DB-konto i [Azure Portal](http://portal.azure.com/), klicka på **Nycklar** och därefter på **Läs- och skrivnycklar**. Använd kopieringsknapparna till höger på skärmen och kopiera URI:n och primärnyckeln till filen app.config i nästa steg.

    ![Visa och kopiera en åtkomstnyckel i Azure Portal, bladet Nycklar](./media/create-documentdb-dotnet-core/keys.png)

2. Öppna filen app.config i Visual Studio. 

3. Kopiera kontonamnet för Azure Cosmos DB från portalen och använd det som värde för AccountName i strängvärdet PremiumStorageConnection i app.config. I skärmbilden ovan är kontonamnet cosmos-db-quickstart. Kontonamnet visas överst i portalen.

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`

4. Kopiera sedan PRIMÄRNYCKEL-värdet från portalen och använd det som värde för AccountKey i PremiumStorageConnectionString. 

    `AccountKey=AUTHKEY`

5. Kopiera slutligen URI-värdet från sidan Nycklar i portalen (med kopieringsknappen) och använd det som värde för TableEndpoint i PremiumStorageConnectionString.

    `TableEndpoint=https://COSMOSDB.documents.azure.com`

    Du kan lämna värdet för StandardStorageConnectionString.

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

## <a name="run-the-web-app"></a>Kör webbappen

1. I Visual Studio högerklickar du på projektet i **Solution Explorer** och därefter på **Hantera NuGet-paket**. 

2. I NuGet-rutan **Bläddra** skriver du *WindowsAzure.Storage* och markerar kryssrutan **Include prerelease** (Ta med förhandsversioner). 

3. Installera biblioteket **WindowsAzure.Storage** från resultaten. Det här installerar förhandsversionen av tabell-API:t i Azure Cosmos DB samt alla beroenden.

4. Tryck på Ctrl + F5 för att köra programmet.

    Data som läggs till i tabellen visas i konsolfönstret. Stäng konsolfönstret när skriptet har körts. 

Du kan nu gå tillbaka till datautforskaren och ställa frågor, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen tar du bort alla resurser som skapades i snabbstarten i Azure Portal med följande steg: 

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en tabell med datautforskaren och att köra en app.  Du kan nu ställa frågor mot dina data med tabell-API:t.  

> [!div class="nextstepaction"]
> [Ställa frågor med tabell-API:t](tutorial-query-table.md)


