---
title: 'Snabbstart: Tabell API med Python - Cosmos Azure DB | Microsoft Docs'
description: "Den här snabbstarten visar hur du använder Azure Cosmos DB tabell API för att skapa ett program med Azure-portalen och Python"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 0900ec1931cc622339133393b72b558076a42710
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Snabbstart: Skapa en tabell med Python och Azure Cosmos DB-API-app

Den här snabbstarten visar hur du använder Python och Azure Cosmos DB [tabell API](table-introduction.md) att skapa en app genom att klona ett exempel från GitHub. Den här snabbstarten visar även hur du skapar ett konto i Azure Cosmos DB och hur du använder Data Explorer för att skapa tabeller och de entiteter i webbaserade Azure-portalen.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och fråga om dokumentet, nyckel/värde-, wide kolumner och graph-databaser som dra nytta av de globala distribution och skala horisontellt kärnan i Azure Cosmos DB. 

## <a name="prerequisites"></a>Krav

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Följande gäller också:

* Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.
* Python Tools för Visual Studio från [GitHub](http://microsoft.github.io/PTVS/). I den här självstudien används Python Tools för VS 2015.
* Python 2.7 från [python.org](https://www.python.org/downloads/release/python-2712/)

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
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Öppna därefter lösningsfilen i Visual Studio. 

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. Detta gör att din app för att kommunicera med databasen värdbaserade. 

1. I den [Azure-portalen](http://portal.azure.com/), klickar du på **anslutningssträngen**. 

    ![Visa och kopiera ANSLUTNINGSSTRÄNGEN i fönstret anslutningssträngen](./media/create-table-python/connection-string.png)

2. Öppna filen config.py och kopiera de nödvändiga egenskaperna för anslutningssträngen i konfigurationsfilen.

3. Spara filen config.py.

## <a name="run-the-app"></a>Kör appen

1. I Visual Studio högerklickar du på projektet i **Solution Explorer**, väljer den aktuella Python-miljön och högerklickar.

2. Välj installera Python-paket, och skriv sedan i **azure-lagringstabellen**

3. Tryck på F5 för att köra programmet. Appen visas i din webbläsare. 

Du kan nu gå tillbaka till datautforskaren och se frågan, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en tabell med datautforskaren och att köra en app.  Du kan nu ställa frågor mot dina data med tabell-API:t.  

> [!div class="nextstepaction"]
> [Importera tabelldata tabell-API: et](table-import.md)
