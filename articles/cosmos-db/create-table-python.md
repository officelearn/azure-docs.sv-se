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
ms.openlocfilehash: 9de9739efce13fc96bf550759eb0ef68d732af1e
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212730"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Snabbstart: Skapa en tabell-API-app med Python och Azure Cosmos DB

> [!div class="op_single_selector"]
> * [NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

I den här snabb starten skapar du och hanterar ett Azure Cosmos DB Tabell-API konto från Azure Portal och från Visual Studio med en python-app klonad från GitHub. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med en URI för `https://localhost:8081` och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)med arbets belastningarna **Azure Development** och **python Development** som valts under installationen. 
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Skapa ett databaskonto

> [!IMPORTANT] 
> Du måste skapa ett nytt tabell-API-konto för att arbeta med de allmänt tillgängliga tabell-API SDK:erna. Tabell-API-konton som skapas i förhandsversionen stöds inte av de allmänt tillgängliga SDK:erna.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Lägg till en tabell

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Lägg till exempeldata

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

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen. På så vis kan appen kommunicera med den värdbaserade databasen. 

1. Välj **anslutnings sträng**i Azure Cosmos DB-kontot i [Azure Portal](https://portal.azure.com/). 

    ![Visa och kopiera ANSLUTNINGS STRÄNGEN i inställningarna för anslutnings strängen](./media/create-table-python/view-and-copy-connection-string-in-connection-string-settings.png)

2. Kopiera KONTONAMN med knappen till höger.

3. Öppna filen *config.py* och klistra in konto namnet från portalen till STORAGE_ACCOUNT_NAME svärdet på rad 19.

4. Gå tillbaka till portalen och kopiera PRIMÄRNYCKEL.

5. Klistra in PRIMÄRNYCKEL från portalen i värdet STORAGE_ACCOUNT_KEY på rad 20.

6. Spara filen *config.py* .

## <a name="run-the-app"></a>Kör appen

1. I Visual Studio högerklickar du på projektet i **Solution Explorer**.

2. Välj den aktuella python-miljön och högerklicka sedan.

2. Välj **Installera python-paket**och ange sedan *Azure-Storage-Table*.

3. Tryck på F5 för att köra appen. Appen visas i din webbläsare. 

Du kan nu gå tillbaka till datautforskaren och se frågan, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en tabell med hjälp av Datautforskaren och kör en python-app i Visual Studio för att lägga till tabell data.  Du kan nu ställa frågor mot dina data med tabell-API:t.  

> [!div class="nextstepaction"]
> [Importera tabelldata till tabell-API](table-import.md)
