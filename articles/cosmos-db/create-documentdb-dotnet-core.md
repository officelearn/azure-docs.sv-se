---
title: 'Azure Cosmos DB: skapa en webbapp med .NET Core och DocumentDB-API:n | Microsoft Docs'
description: "Presenterar ett .NET Core-kodexempel som du kan använda för att ansluta till och fråga Azure Cosmos DB DocumentDB-API:n"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 52cf1a729e4e86f764f9ded3712eaba558f1fc7f
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-documentdb-api-web-app-with-net-core-and-the-azure-portal"></a>Azure Cosmos DB: skapa en DocumentDB-API-webbapp med .NET Core och Azure Portal

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

I den här snabbstarten visas hur du skapar ett Azure Cosmos DB-konto, en dokumentdatabas och en samling med hjälp av Azure Portal. Därefter skapar och distribuerar du en webbapp med en att göra-lista som bygger på [DocumentDB .NET Core-API:n](../documentdb/documentdb-introduction.md)som visas i följande skärmbild. 

![Att göra-app med exempeldata](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

## <a name="prerequisites"></a>Krav

Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-net-core"></a>Installera .NET Core

Installera .NET Core med hjälp av instruktionerna på sidan [.NET Core SDK](https://www.microsoft.com/net/download/core). SDK: er är tillgängliga för Windows, macOS och Linux.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="add-a-collection"></a>Lägga till en samling

[!INCLUDE [cosmosdb-create-collection](../../includes/cosmosdb-create-collection.md)]

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmosdb-create-sample-data](../../includes/cosmosdb-create-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en DocumentDB-API-app från github, ange anslutningssträngen och köra den. Du kommer att se hur lätt det är att arbeta med data programmässigt.

1. Öppna ett git-terminalfönster, till exempel git bash, och `CD` till en arbetskatalog.  

2. Klona exempellagringsplatsen med följande kommando. 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. Öppna därefter lösningsfilen i Visual Studio. 
    
## <a name="review-the-code"></a>Granska koden

[!INCLUDE [cosmosdb-tutorial-review-code-dotnet](../../includes/cosmosdb-tutorial-review-code-dotnet.md)]

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen.

1. I [Azure Portal](http://portal.azure.com/) går du till ditt Azure Cosmos DB-konto. Klicka på **Nycklar** i det västra navigeringsfönstret och sedan på **läs- och skrivnycklar**. Du använder kopiera-knapparna till höger på skärmen för att kopiera URI:n och primärnyckeln i web.config-filen i nästa steg.

    ![Visa och kopiera åtkomstnyckeln i Azure Portal, bladet Nycklar](./media/create-documentdb-dotnet-core/keys.png)

2. I Visual Studio 2017 öppnar du web.config-filen. 

3. Kopiera ditt URI-värde från portalen (med kopieringsknappen) och gör det till värdet för slutpunktsnyckeln i web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Kopiera sedan värdet för primärnyckeln från portalen och gör det till värdet för authKey i web.confif. 

    `<add key="authKey" value="FILLME" />`

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

## <a name="run-the-web-app"></a>Kör webbappen

1. I Visual Studio högerklickar du på projektet i **Solution Explorer** och därefter på **Hantera NuGet-paket**. 

2. I NuGet-rutan **Bläddra** skriver du in *DocumentDB*.

3. Från resultatet installerar du **Microsoft Azure DocumentDB**-biblioteket. Det här installerar Azure Cosmos DB-paketet samt alla beroenden.

4. Tryck på Ctrl + F5 för att köra programmet. Appen visas i din webbläsare. 

5. Klicka på **Skapa nytt** i webbläsaren och skapa några nya uppgifter i din att göra-app.

   ![Att göra-app med exempeldata](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

Du kan nu gå tillbaka till datautforskaren och se frågan, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen tar du bort alla resurser som skapades i snabbstarten i Azure Portal med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur man skapar ett Azure Cosmos DB-konto, skapar en samling med datautforskaren och kör en webbapp. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](../documentdb/documentdb-import-data.md)

