---
title: 'Snabbstart: Tabell API med .NET - Cosmos Azure DB | Microsoft Docs'
description: "Den här snabbstarten visar hur du använder Azure Cosmos DB tabell API för att skapa ett program med Azure-portalen och .NET"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quickstart connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/16/2017
ms.author: mimig
ms.openlocfilehash: 4e59c333e14e5e21a02c3160cf6311d1182e5a5e
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="quickstart-build-a-table-api-app-with-net-and-azure-cosmos-db"></a>Snabbstart: Skapa en tabell med .NET och Azure Cosmos DB-API-app 

Den här snabbstarten visar hur du använder Java och Azure Cosmos DB [tabell API](table-introduction.md) att skapa en app genom att klona ett exempel från GitHub. Den här snabbstarten visar även hur du skapar ett konto i Azure Cosmos DB och hur du använder Data Explorer för att skapa tabeller och de entiteter i webbaserade Azure-portalen.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

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
2. Nu lägga till data i rutorna PartitionKey värderutan och RowKey värdet och klicka på **lägga till entiteten**.

   ![Ange partitionsnyckel och radnyckel för en ny entitet](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Du kan nu lägga till fler entiteter i din tabell, redigera entiteter eller ställa frågor mot data i Datautforskaren. I Datautforskaren kan du även skala ditt dataflöde och lägga till lagrade procedurer, användardefinierade funktioner och utlösare i tabellen.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Table-app från github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en git-terminalfönster, till exempel git bash och använda den `cd` kommando för att ändra till en mapp att installera sample-appen. 

    ```bash
    cd "C:\git-samples"
    ```

2. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av sample-appen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Öppna sedan lösningsfilen TableStorage i Visual Studio. 

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. Detta gör att din app för att kommunicera med databasen värdbaserade. 

1. I den [Azure-portalen](http://portal.azure.com/), klickar du på **anslutningssträngen**. 

    Använd knapparna Kopiera på höger sida av skärmen för att kopiera primära ANSLUTNINGSSTRÄNGEN.

    ![Visa och kopiera primära ANSLUTNINGSSTRÄNGEN i fönstret anslutningssträngen](./media/create-table-dotnet/connection-string.png)

2. Öppna filen App.config i Visual Studio. 

3. Ta bort kommentarerna StorageConnectionString på rad 8 och kommentera ut StorageConnectionString på rad 7 som den här kursen inte används av Storage-emulatorn. Rad 7 och 8 bör nu se ut så här:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Klistra in primära ANSLUTNINGSSTRÄNGEN från portalen i StorageConnectionString värdet på rad 8. Klistra in sträng inom citattecken. Om din slutpunkt använder documents.azure.com, ändrar du den del till table.cosmosdb.azure.com. 

    Rad 8 bör nu se ut:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

4. Spara filen App.config.

Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Skapa och distribuera appen

1. I Visual Studio högerklickar du på den **TableStorage** projektet i **Solution Explorer** och klicka sedan på **hantera NuGet-paket**. 

2. I NuGet **Bläddra** skriver *Microsoft.Azure.CosmosDB.Table*.

3. Från resultatet installerar den **Microsoft.Azure.CosmosDB.Table** bibliotek. Detta installerar Azure Cosmos DB tabell API-paketet, samt alla beroenden.

4. Öppna BasicSamples.cs och Lägg till en brytpunkt 30 och 52.

5. Tryck på Ctrl + F5 för att köra programmet.

    Konsolfönstret visar den informationen läggs till i den nya tabell databasen i Azure Cosmos-databasen. 
    
    Om du får ett felmeddelande om beroenden finns [felsökning](table-sdk-dotnet.md#troubleshooting).

    När du klickar på den första brytpunkten, gå tillbaka till Data Explorer i Azure-portalen och utöka tabellen demo * och på **entiteter**. Den **entiteter** flik till höger visas den nya entiteten som lades till, Observera att telefonnummer för användaren är 425-555-0101.
    
6. Stäng fliken entiteter i Data Explorer.
    
7. Fortsätta att köra appen till nästa brytpunkt.

    När du träffar brytpunkten växla tillbaka till portalen, klicka på entiteter igen för att öppna fliken entiteter och Observera att telefonnumret har uppdaterats till 425-555-0105.

8. Tryck på CTRL + C för att avsluta körningen av appen tillbaka i konsolfönstret. 

    Du kan nu gå tillbaka till Data Explorer och Lägg till eller ändra entiteter och fråga efter data.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en tabell med datautforskaren och att köra en app.  Du kan nu ställa frågor mot dina data med tabell-API:t.  

> [!div class="nextstepaction"]
> [Importera tabelldata tabell-API: et](table-import.md)

