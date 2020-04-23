---
title: 'Snabbstart: Tabell-API med Python – Azure Cosmos DB'
description: Den här snabbstarten visar hur du använder tabell-API i Azure Cosmos DB för att skapa ett program med Azure-portalen och Python
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-python-october2019
ms.openlocfilehash: 673155706c3b7d44b41562c2dc8ce24a94ac6a58
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083639"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Snabbstart: Skapa en tabell-API-app med Python och Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB Table API-konto från Azure-portalen och från Visual Studio med en Python-app klonad från GitHub. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärde- och diagramdatabaser med globala distributions- och vågräta skalfunktioner.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) med en URI av `https://localhost:8081` och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Visual Studio 2019](https://www.visualstudio.com/downloads/), med **Azure-utvecklings-** och **Python-utvecklingsarbetsbelastningar** valda under installationen. 
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Skapa ett databaskonto

> [!IMPORTANT] 
> Du måste skapa ett nytt tabell-API-konto för att arbeta med de allmänt tillgängliga tabell-API SDK:erna. Tabell-API-konton som skapas i förhandsversionen stöds inte av de allmänt tillgängliga SDK:erna.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Lägg till en tabell

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Table-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

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
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Öppna därefter lösningsfilen i Visual Studio. 

> ! -Jag har inte tid med det här. En mer detaljerad genomgång av liknande kod finns i [exempelartikeln för Cosmos DB Table API.](table-storage-how-to-use-python.md)

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. På så vis kan appen kommunicera med den värdbaserade databasen. 

1. Välj **Anslutningssträng**i ditt Azure Cosmos DB-konto i [Azure-portalen](https://portal.azure.com/). 

    ![Visa och kopiera ANSLUTNINGSSTRÄNGEN i anslutningsstränginställningarna](./media/create-table-python/view-and-copy-connection-string-in-connection-string-settings.png)

2. Kopiera KONTONAMN med knappen till höger.

3. Öppna *config.py-filen* och klistra in ACCOUNT NAME från portalen till STORAGE_ACCOUNT_NAME värdet på rad 19.

4. Gå tillbaka till portalen och kopiera PRIMÄRNYCKEL.

5. Klistra in PRIMÄRNYCKEL från portalen i värdet STORAGE_ACCOUNT_KEY på rad 20.

6. Spara *config.py-filen.*

## <a name="run-the-app"></a>Kör appen

1. Högerklicka på projektet i **Solution Explorer**i Visual Studio .

2. Markera den aktuella Python-miljön och högerklicka sedan.

2. Välj **Installera Python-paket**och ange sedan *azure-storage-table*.

3. Tryck på F5 för att köra appen. Appen visas i din webbläsare. 

Du kan nu gå tillbaka till datautforskaren och se frågan, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en tabell med Data Explorer och kör en Python-app i Visual Studio för att lägga till tabelldata.  Du kan nu ställa frågor mot dina data med tabell-API:t.  

> [!div class="nextstepaction"]
> [Importera tabelldata till tabell-API](table-import.md)
