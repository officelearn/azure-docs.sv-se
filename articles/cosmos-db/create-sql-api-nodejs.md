---
title: 'Snabb start: Använd Node. js för att fråga från Azure Cosmos DB SQL API-konto'
description: Hur du använder Node. js för att skapa en app som ansluter till Azure Cosmos DB SQL API-konto och skickar frågor till data.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: d8b17472bb531ec799be227706261962d7914d68
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134455"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Snabb start: Använd Node. js för att ansluta och fråga efter data från Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabb starten skapar du och hanterar ett Azure Cosmos DB SQL API-konto från Azure Portal och använder en Node. js-app som klonas från GitHub. Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med en URI för `https://localhost:8081` och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node. js-6.0.0 +](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-a-database"></a>Skapa en databas 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Lägg till en behållare

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Lägg till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Node. js-app från GitHub, ange anslutnings strängen och köra den.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du är intresse rad av att lära dig hur Azure Cosmos Database-resurserna skapas i koden kan du läsa följande kodfragment. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

Om du är bekant med den tidigare versionen av SQL JavaScript SDK kan du använda den för att Visa villkors *samlingen* och *dokumentet*. Eftersom Azure Cosmos DB stöder [flera API-modeller](introduction.md), använder [version 2.0 + av JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) en *behållare*för generiska termer, som kan vara en samling, Graf eller tabell och *objekt* för att beskriva behållarens innehåll.

Följande kodfragment är alla hämtade från filen *app.js*.

* `CosmosClient`-objektet har initierats.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Skapa en ny Azure Cosmos-databas.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* En ny behållare (samling) skapas i-databasen.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Ett objekt (dokument) skapas.

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* En SQL-fråga över JSON utförs på familje databasen. Frågan returnerar alla underordnade till "Anderson"-serien. 

    ```javascript
      const querySpec = {
        query: 'SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName',
        parameters: [
          {
            name: '@lastName',
            value: 'Andersen'
          }
        ]
      }

      const { resources: results } = await client
        .database(databaseId)
        .container(containerId)
        .items.query(querySpec)
        .fetchAll()
      for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult)
        console.log(`\tQuery returned ${resultString}\n`)
      }
    ```    

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå tillbaka till Azure Portal för att hämta information om anslutnings strängen för ditt Azure Cosmos-konto. Kopiera anslutnings strängen till appen så att den kan ansluta till databasen.

1. I ditt Azure Cosmos DB konto i [Azure Portal](https://portal.azure.com/)väljer du **nycklar** i det vänstra navigerings fältet och väljer sedan **Läs-och skriv nycklar**. Använd kopierings knapparna till höger på skärmen för att kopiera URI: n och primär nyckeln till filen *config. js* i nästa steg.

    ![Visa och kopiera åtkomstnyckeln i Azure Portal, bladet Nycklar](./media/create-sql-api-dotnet/keys.png)

2. I öppna filen *config. js* . 

3. Kopiera ditt URI-värde från portalen (med kopierings knappen) och gör det till värdet för slut punkts nyckeln i *config. js*. 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. Kopiera sedan värdet för primär nyckeln från portalen och gör det till värdet för `config.key` i *config. js*. Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>Kör appen

1. Kör `npm install` i en terminal för att installera de npm-moduler som krävs

2. Kör `node app.js` i en terminal för att starta nodprogrammet.

Du kan nu gå tillbaka till Datautforskaren, ändra och arbeta med dessa nya data.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-konto, skapar en behållare med hjälp av Datautforskaren och kör en Node. js-app. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)


