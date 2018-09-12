---
title: 'Azure Cosmos DB: Skapa en app med Node.js och SQL API:t | Microsoft Docs'
description: Presenterar ett Node.js-kodexempel som du kan använda för att ansluta till och ställa frågor via Azure Cosmos DB SQL API:t
services: cosmos-db
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/30/2018
ms.author: dech
ms.openlocfilehash: e6455e5417828b51921fdf608dc95d76154162d6
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698663"
---
# <a name="azure-cosmos-db-build-a-nodejs-app-using-sql-api-javascript-sdk-20-preview-and-the-azure-portal"></a>Azure Cosmos DB: Skapa en Node.js-app med SQL API, JavaScript SDK 2.0 (förhandsversion) och Azure-portalen 

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Node.js- v2](create-sql-api-nodejs-preview.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här snabbstarten visar hur du skapar ett [SQL API](sql-api-introduction.md)-konto i Azure Cosmos DB, en dokumentdatabas och en container med Azure-portalen. Sedan skapar du och kör en konsolapp som är byggd med [SQL JavaScript SDK](sql-api-sdk-node.md). Den här snabbstarten använder version 2.0 av [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos), vilket för närvarande är i förhandsversion. 

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Följande gäller också:
    * [Node.js](https://nodejs.org/en/) version v6.0.0 eller senare
    * [Git](http://git-scm.com/)

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Lägga till en samling

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Lägg till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona vi en SQL API-app från Github, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

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

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

Observera att om du är bekant med den tidigare versionen av JavaScript SDK så är du kanske van vid att se termerna ”samling” och ”dokument”. Eftersom Azure Cosmos DB stöder [flera API-modeller](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities) använder version 2.0+ av JavaScript SDK de allmänna termerna ”container”, vilket kan vara en samling, ett diagram eller en tabell, och ”objekt” för att beskriva innehållet i containern.

Följande kodfragment är alla hämtade från filen **app.js**.

* `CosmosClient` har initierats.

    ```nodejs
    const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
    ```

* En ny databas skapas.

    ```nodejs
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* En ny container (samling) skapas på.

    ```nodejs
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Ett objekt (dokument) skapas.

    ```nodejs
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* En SQL-fråga över JSON utförs.

    ```nodejs
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
    ```    

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen.

1. I [Azure Portal](http://portal.azure.com/) går du till ditt Azure Cosmos DB-konto. Klicka på **Nycklar** i det västra navigeringsfönstret och sedan på **läs- och skrivnycklar**. Du använder kopiera-knapparna till höger på skärmen för att kopiera URI:n och primärnyckeln i `config.js`-filen i nästa steg.

    ![Visa och kopiera åtkomstnyckeln i Azure Portal, bladet Nycklar](./media/create-sql-api-dotnet/keys.png)

2. Öppna filen `config.js`. 

3. Kopiera ditt URI-värde från portalen (med kopieringsknappen) och gör det till värdet för slutpunktsnyckeln i `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Kopiera sedan värdet för primärnyckeln från portalen och gör det till värdet för `config.primaryKey` i `config.js`. Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. 

    `config.primaryKey = "FILLME"`
    
## <a name="run-the-app"></a>Kör appen
1. Kör `npm install` i en terminal för att installera de npm-moduler som krävs

2. Kör `node app.js` i en terminal för att starta nodprogrammet.

Du kan nu gå tillbaka till datautforskaren och se frågan, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur man skapar ett Azure Cosmos DB-konto, skapar en samling med datautforskaren och kör en app. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)


