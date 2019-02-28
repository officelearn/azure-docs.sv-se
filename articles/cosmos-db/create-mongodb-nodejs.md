---
title: Ansluta en Node.js MongoDB-app till Azure Cosmos DB
description: Den här snabbstarten visar hur du ansluter en befintlig MongoDB-app som skrivits i Node.js till Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 12/06/2018
ms.openlocfilehash: 99f6de4d0441594c09e5e8c29834b90aeb1d43a6
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586486"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Snabbstart: Migrera en befintlig MongoDB Node.js-webbapp till Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Cosmos DB. 

Den här snabbstarten visar hur du använder en befintlig MongoDB-app som skrivits i Node.js och ansluter den till din Cosmos-databas, som stöder MongoDB-klient. Med andra ord är det tydligt för programmet att data lagras i en Cosmos-databas.

När du är klar har du ett MEAN-program (MongoDB, Express, Angular och Node.js) som körs på [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

![MEAN.js-app som körs i Azure App Service](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Nödvändiga komponenter 
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Utöver Azure CLI, behöver du [Node.js](https://nodejs.org/) och [Git](https://www.git-scm.com/downloads) installerat lokalt om du vill köra `npm` och `git` kommandon.

Du bör ha kunskaper inom Node.js. Den här snabbstarten är inte avsedd att hjälpa dig med att utveckla Node.js-program i allmänhet.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Kör följande kommandon för att klona exempellagringsplatsen. Den här exempellagringsplatsen innehåller standard-[MEAN.js](https://meanjs.org/)-programmet.

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
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Köra programmet

Installera de nödvändiga paketen och starta programmet.

```bash
cd mean
npm install
npm start
```
Programmet försöker att ansluta till en MongoDB-källa, vilket misslyckas. Gå vidare och avsluta programmet när utdata returnerar ”[MongoError: connect ECONNREFUSED 127.0.0.1:27017]”.

## <a name="log-in-to-azure"></a>Logga in på Azure

Om du använder en installerad Azure CLI loggar du in på Azure-prenumerationen med kommandot [az login](/cli/azure/reference-index#az-login) och följer anvisningarna på skärmen. Du kan hoppa över det här steget om du använder Azure Cloud-gränssnittet.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Lägg till Azure Cosmos DB-modulen

Om du använder en installerad Azure CLI, kontrollera om `cosmosdb`-komponenten har installerats genom att köra kommandot `az`. Om `cosmosdb` är i listan över grundläggande kommandon, fortsätter du med nästa kommando. Du kan hoppa över det här steget om du använder Azure Cloud-gränssnittet.

Om `cosmosdb` inte är i listan över grundläggande kommandon, installerar du om [Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [resursgrupp](../azure-resource-manager/resource-group-overview.md) med [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container som Azure-resurser (t.ex. webbappar, databaser och lagringskonton) distribueras och hanteras i. 

Följande exempel skapar en resursgrupp i regionen västeuropa. Välj ett unikt namn för resursgruppen.

Om du använder Azure Cloud Shell, klickar du på **Prova**, följer anvisningarna på skärmen för att logga in och kopierar sedan kommandot i Kommandotolken.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Skapa ett Cosmos-konto med kommandot [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

I följande kommando ersätter du ditt eget unika Cosmos-kontonamn där du ser platshållaren `<cosmosdb-name>`. Den här unika namnet kommer att användas som en del av din Cosmos DB-slutpunkt (`https://<cosmosdb-name>.documents.azure.com/`) så namnet måste vara unikt för alla Cosmos-konton i Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Parametern `--kind MongoDB` aktiverar MongoDB-klientanslutningar.

När Azure Cosmos DB-kontot har skapats, visar Azure CLI information liknande följande exempel. 

> [!NOTE]
> I det här exemplet används JSON som standardalternativ för Azure CLI-utdataformat. Om du vill använda andra format läser du [Utdataformat för Azure CLI-kommandon](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Anslut ditt Node.js-program till databasen

I det här steget ansluter du ditt MEAN.js-exempelprogram till en Cosmos-databas som du just skapade. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurera anslutningssträngen i ditt Node.js-program

I din MEAN.js-lagringsplats, öppnar du `config/env/local-development.js`.

Ersätt innehållet i filen med följande kod. Se till att även ersätta de två `<cosmosdb-name>`-platshållarna med namnet på ditt Cosmos-konto.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Hämta nyckeln

För att kunna ansluta till en Cosmos-databas behöver du databasnyckeln. Använd kommandot [az documentdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys) för att hämta den primära nyckeln.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI matar ut information som liknar följande exempel. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Kopiera värdet för `primaryMasterKey`. Klistra in det över `<primary_master_key>` i `local-development.js`.

Spara ändringarna.

### <a name="run-the-application-again"></a>Kör programmet igen.

Kör `npm start` igen. 

```bash
npm start
```

Ett konsolmeddelande borde nu komma upp som säger att utvecklingsmiljön är klar och igång. 

Gå till `http://localhost:3000` i en webbläsare. Klicka på **registrera dig** i huvudmenyn och försök skapa två låtsasanvändare. 

MEAN.js-exempelprogrammet lagrar användardata i databasen. Om du har lyckats och MEAN.js automatiskt loggar in på den skapade användaren så fungerar din Azure Cosmos DB-anslutning. 

![MEAN.js ansluter till MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Visa data i datautforskaren

Data som lagras i en Cosmos-databas är tillgängliga att visa och fråga i Azure-portalen.

Om du vill visa, fråga och arbeta med användardata som skapats i föregående steg, loggar du in på [Azure-portalen](https://portal.azure.com) i din webbläsare.

I den övre sökrutan skriver du in Azure Cosmos DB. När ditt Cosmos-kontoblad öppnas väljer du ditt Cosmos-konto. I det vänstra navigeringsfönstret, klickar du på datautforskaren. Utöka din samling i samlings-fönstret så kan du visa dokumenten i samlingen, fråga data och skapa och köra lagrade procedurer, utlösare och UDF:er. 

![Datautforskaren i Azure-portalen](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Distribuera Node.js-programmet till Azure

I det här steget distribuerar du ditt Node.js-program till Cosmos DB.

Du kanske har märkt att konfigurationsfilen du ändrade tidigare är för utvecklingsmiljön (`/config/env/local-development.js`). När du distribuerar ditt program till App Service, körs det i produktionsmiljön som standard. Nu behöver du göra samma ändring till respektive konfigurationsfil.

I din MEAN.js-lagringsplats, öppnar du `config/env/production.js`.

I objektet `db`, ersätter du värdet `uri` som det visas i följande exempel. Se till att ersätta platshållarna som innan.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Alternativet `ssl=true` är viktigt eftersom [Cosmos DB kräver SSL](connect-mongodb-account.md#connection-string-requirements). 
>
>

Spara dina ändringar till Git i terminalen. Du kan kopiera bägge kommandona för att köra dem tillsammans.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Cosmos-konto, skapar en samling och kör en konsolapp. Du kan nu importera ytterligare data till din Cosmos-databas. 

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](mongodb-migrate.md)
