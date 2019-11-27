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
ms.openlocfilehash: 44cdd4307be56d864afb45d619958cc59a3fa978
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220526"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Snabb start: Använd Node. js för att ansluta och fråga efter data från Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Den här snabb starten visar hur du använder en Node. js-app för att ansluta till [SQL API](sql-api-introduction.md) -kontot i Azure Cosmos dB. Du kan sedan använda Azure Cosmos DB SQL-frågor för att fråga och hantera data. Node. js-appen som du skapar i den här artikeln använder [SQL JavaScript SDK](sql-api-sdk-node.md). I den här snabbstarten används version 2.0 av [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Krav

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Följande gäller också:
    * [Node.js](https://nodejs.org/en/) version v6.0.0 eller senare
    * [Git](https://git-scm.com/)

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

Observera att om du är bekant med den tidigare versionen av JavaScript SDK så är du kanske van vid att se termerna ”samling” och ”dokument”. Eftersom Azure Cosmos DB stöder [flera API-modeller](https://docs.microsoft.com/azure/cosmos-db/introduction) använder version 2.0+ av JavaScript SDK de allmänna termerna ”container”, vilket kan vara en samling, ett diagram eller en tabell, och ”objekt” för att beskriva innehållet i containern.

Följande kodfragment är alla hämtade från filen **app.js**.

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

1. I det [Azure Portal](https://portal.azure.com/)går du till ditt Azure Cosmos-konto, i det vänstra navigerings fönstret, klickar på **nycklar**och klickar sedan på **Läs-och skriv nycklar**. Du använder kopiera-knapparna till höger på skärmen för att kopiera URI:n och primärnyckeln i `config.js`-filen i nästa steg.

    ![Visa och kopiera en åtkomstnyckel i Azure Portal, bladet Nycklar](./media/create-sql-api-dotnet/keys.png)

2. Öppna filen `config.js`. 

3. Kopiera ditt URI-värde från portalen (med kopieringsknappen) och gör det till värdet för slutpunktsnyckeln i `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Kopiera sedan värdet för primärnyckeln från portalen och gör det till värdet för `config.key` i `config.js`. Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

    `config.key = "FILLME"`
    
## <a name="run-the-app"></a>Kör appen

1. Kör `npm install` i en terminal för att installera de npm-moduler som krävs

2. Kör `node app.js` i en terminal för att starta nodprogrammet.

Du kan nu gå tillbaka till Datautforskaren, ändra och arbeta med dessa nya data.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos-konto, skapar en behållare med data Utforskaren och kör en app. Nu kan du importera ytterligare data till Azure Cosmos-databasen. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)


