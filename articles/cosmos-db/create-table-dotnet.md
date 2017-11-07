---
title: Skapa en .NET-app i Azure Cosmos DB med tabell-API:t | Microsoft Docs
description: "Kom igång med tabell-API:t i Azure Cosmos DB med hjälp av .NET"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/22/2017
ms.author: arramac
ms.openlocfilehash: 9b1d41fe185f4c3d5fdce13ab8f0136bc961f013
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: Skapa en .NET-app med tabell-API:t

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här snabbstarten demonstrerar hur du skapar ett Azure Cosmos DB-konto och en tabell i kontot med hjälp av Azure Portal. Du sedan skriva kod för att infoga, uppdatera och ta bort entiteter och köra några frågor med nya [Windows Azure Storage Premium Table](https://aka.ms/premiumtablenuget) (förhandsversion) från NuGet. Det här biblioteket har samma klasser och metodsignaturer som den offentliga versionen av [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), men även funktioner för att ansluta till Azure Cosmos DB-konton via [tabell-API:t](table-introduction.md) (förhandsversion). 

## <a name="prerequisites"></a>Krav

Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Lägg till en tabell

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Lägg till exempeldata

Du kan nu lägga till data till den nya tabellen med datautforskaren.

1. Öppna Datautforskaren, expandera **sample-table**, klicka på **Entiteter** och klicka sedan på **Lägg till entitet**.

   ![Skapa nya entiteter i datautforskaren i Azure Portal](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Lägg till data i värderutorna för egenskaperna PartitionKey (Partitionsnyckel) och RowKey (Radnyckel) och klicka på **Lägg till entitet**.

   ![Ange partitionsnyckel och radnyckel för en ny entitet](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Du kan nu lägga till fler entiteter i din tabell, redigera entiteter eller ställa frågor mot data i Datautforskaren. I Datautforskaren kan du även skala ditt dataflöde och lägga till lagrade procedurer, användardefinierade funktioner och utlösare i tabellen.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Table-app från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna ett git-terminalfönster, till exempel git bash, och `cd` till en arbetskatalog.  

2. Klona exempellagringsplatsen med följande kommando. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Öppna därefter lösningsfilen i Visual Studio. 

## <a name="review-the-code"></a>Granska koden

Vi gör en snabb genomgång av vad som händer i appen. Öppna filen Program.cs så ser du att de här kodraderna skapar Azure Cosmos DB-resurserna. 

* CloudTableClient har initierats.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* En ny tabell skapas om det inte finns någon.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* En serie steg utförs på tabellen med de `TableOperation` klass.

   ```csharp
   TableOperation insertOperation = TableOperation.Insert(item);
   table.Execute(insertOperation);
   ```
   
   ```csharp
   TableOperation retrieveOperation = TableOperation.Retrieve<T>(items[i].PartitionKey, items[i].RowKey);
   table.Execute(retrieveOperation);
   ```
   
   ```csharp
   TableOperation deleteOperation = TableOperation.Delete(items[i]);
   table.Execute(deleteOperation);
   ```


## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Nu ska vi uppdatera informationen i anslutningssträngen så att din app kan kommunicera med Azure Cosmos DB. 

1. Öppna filen app.config i Visual Studio. 

2. I [Azure-portalen](http://portal.azure.com/), i det vänstra navigeringsfönstret i Azure Cosmos DB klickar du på **Anslutningssträng**. Klicka på kopieringsknappen för anslutningssträngen i den nya rutan. 

    ![Visa och kopiera slutpunkt och kontonyckel i anslutningssträngfönstret](./media/create-table-dotnet/keys.png)

3. Klistra in värdet i filen app.config som värde för PremiumStorageConnectionString. 

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`    

    Du kan lämna värdet för StandardStorageConnectionString.

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Kör konsolappen

1. I Visual Studio högerklickar du på projektet **PremiumTableGetStarted** i **Solution Explorer** och sedan på **Hantera NuGet-paket**. 

2. I rutan NuGet **Bläddra** skriver du *WindowsAzure.Storage PremiumTable*.

3. Markera rutan **Inkludera förhandsversion**. 

4. Installera biblioteket **WindowsAzure.Storage-PremiumTable** från resultaten. Det här installerar förhandsversionen av tabell-API:t i Azure Cosmos DB samt alla beroenden. Observera att detta är ett annat NuGet-paket än Windows Azure Storage-paketet som används av Azure Table-lagring. 

5. Tryck på Ctrl + F5 för att köra programmet.

    Konsolfönstret visar data som läggs till, hämtas, efterfrågas, ersätts och tas bort från tabellen. Stäng konsolfönstret genom att trycka på valfri tangent när skriptet har körts. 
    
    ![Konsolens utdata i snabbstart](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-console-output.png)

6. Om du vill se nya entiteter i Data Explorer ska du kommentera ut rader 188 208 i program.cs så att de inte raderas och köra exemplet igen. 

    Du kan nu gå tillbaka till Data Explorer, klicka på **Uppdatera**, expandera tabellen **personer** och klicka på **Entiteter**, och sedan arbeta med dessa nya data. 

    ![Nya entiteter i Data Explorer](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen tar du bort alla resurser som skapades i snabbstarten i Azure Portal med följande steg: 

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en tabell med datautforskaren och att köra en app.  Du kan nu ställa frågor mot dina data med tabell-API:t.  

> [!div class="nextstepaction"]
> [Ställa frågor med tabell-API:t](tutorial-query-table.md)

