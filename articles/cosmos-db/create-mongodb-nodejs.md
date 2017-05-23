---
title: "Anslut en MongoDB-app till Azure Cosmos DB med hjälp av Node.js | Microsoft Docs"
description: "Läs hur du ansluter en befintlig Node.js MongoDB app till Azure Cosmos DB"
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
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0e5657e4d110af095c934431cb6e3bf8824f791d
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-migrate-an-existing-nodejs-mongodb-web-app"></a>Azure Cosmos DB: Migrera en befintlig Node.js MongoDB-webbapp 

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här snabbstarten visar hur du använder en befintlig [MongoDB](../documentdb/documentdb-protocol-mongodb.md)-app som skrivits i Node.js och ansluter den till din Azure Cosmos DB-databas, som stöder MongoDB-klientanslutningar. Med andra ord vet ditt Node.js program bara att det ansluter till en databas som använder MongoDB-API:er. Det är transparent för programmet att data lagras i Azure Cosmos DB.

När du är klar har du ett MEAN-program (MongoDB, Express, AngularJS och Node.js) som kör på [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). 

![MEAN.js-app som körs i Azure App Service](./media/create-mongodb-nodejs/meanjs-in-azure.png)

## <a name="prerequisites"></a>Krav 

Innan du startar den här snabbstarten se till att [Azure CLI är installerat](https://docs.microsoft.com/cli/azure/install-azure-cli) på datorn. Dessutom behöver du [Node.js](https://nodejs.org/) och [Git](http://www.git-scm.com/downloads). Du kommer att köra kommandona `az`, `npm` och `git`.

Du bör ha kunskaper inom Node.js. Den här snabbstarten är inte avsedd att hjälpa dig med att utveckla Node.js-program i allmänhet.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Öppna ett git-terminalfönster, till exempel git bash och `cd` till en arbetskatalog.  

Kör följande kommandon för att klona exempellagringsplatsen. Den här exempellagringsplatsen innehåller standard-[MEAN.js](http://meanjs.org/)-programmet. 

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

## <a name="log-in-to-azure"></a>Logga in på Azure

Nu ska vi använda Azure CLI 2.0 i ett terminalfönster för att skapa de resurser som behövs för att ha ditt Node.js-program i Azure App Service.  Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen. 

```azurecli 
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Lägg till Azure Cosmos DB-modulen

För att använda Azure Cosmos DB-kommandon, behöver du lägga till Azure Cosmos DB-modulen. 

```azurecli
az component update --add cosmosdb
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [resursgrupp](../azure-resource-manager/resource-group-overview.md) med [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare som Azure-resurser (t.ex. webbappar, databaser och lagringskonton) distribueras och hanteras i. 

Följande exempel skapar en resursgrupp i regionen västeuropa. Välj ett unikt namn för resursgruppen.

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Skapa ett Azure Cosmos DB-konto med kommandot [az cosmosdb create](/cli/azure/cosmosdb#create).

I följande kommando, ersätter du ditt eget unika Azure Cosmos DB-kontonamn där du ser platshållaren `<cosmosdb_name>`. Den här unika namnet kommer att användas som en del av din Azure Cosmos DB-slutpunkt (`https://<cosmosdb_name>.documents.azure.com/`) så namnet måste vara unikt för alla Azure Cosmos DB-konton i Azure. 

```azurecli
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

Parametern `--kind MongoDB` aktiverar MongoDB-klientanslutningar.

När Azure Cosmos DB-kontot har skapats, visar Azure CLI information liknande följande exempel. 

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb_name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb_name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb_name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb_name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb_name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb_name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Anslut ditt Node.js-program till databasen

I det här steget, ansluter du ditt MEAN.js-exempelprogram till en Azure Cosmos DB-databas som du just skapade med en MongoDB-anslutningssträng. 

## <a name="retrieve-the-key"></a>Hämta nyckeln

För att kunna ansluta till en Azure Cosmos DB-databas, behöver du databasnyckeln. Använd kommandot [az documentdb list-keys](/cli/azure/documentdb#list-keys) för att hämta den primära nyckeln.

```azurecli
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

Azure CLI matar ut information som liknar följande exempel. 

```json
{
  "primaryMasterKey": "RUayjYjixJDWG5xTqIiXjC...",
  "primaryReadonlyMasterKey": "...",
  "secondaryMasterKey": "...",
  "secondaryReadonlyMasterKey": "..."
}
```

Kopiera värdet för `primaryMasterKey` till en textredigerare. Du behöver den här informationen i nästa steg.

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurera anslutningssträngen i ditt Node.js-program

I din MEAN.js-lagringsplats, öppnar du `config/env/local-development.js`.

Ersätt innehållet i filen med följande kod. Se även till att ersätta de två platshållarna `<cosmosdb_name>` med ditt Azure Cosmos DB-kontonamn och platshållaren `<primary_master_key>` med nyckeln som du kopierade i föregående steg.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

> [!NOTE] 
> Alternativet `ssl=true` är viktigt eftersom [Azure Cosmos DB kräver SSL](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
>
>

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

Data som lagras av en Azure Cosmos DB finns tillgänglig för att se, fråga och köra affärslogik på i Azure-portalen.

Om du vill visa, fråga och arbeta med användardata som skapats i föregående steg, loggar du in på [Azure-portalen](https://portal.azure.com) i din webbläsare.

I den övre sökrutan skriver du in Azure Cosmos DB. När ditt Cosmos DB-kontoblad öppnas, väljer du ditt Cosmos DB-konto. I det vänstra navigeringsfönstret, klickar du på datautforskaren. Utöka din samling i samlings-fönstret så kan du visa dokumenten i samlingen, fråga data och skapa och köra lagrade procedurer, utlösare och UDF:er. 

![Datautforskaren i Azure-portalen](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Distribuera Node.js-programmet till Azure

I det här steget, distribuerar du ditt MongoDB-anslutna Node.js-program till Azure Cosmos DB.

Du kanske har märkt att konfigurationsfilen du ändrade tidigare är för utvecklingsmiljön (`/config/env/local-development.js`). När du distribuerar ditt program till App Service, körs det i produktionsmiljön som standard. Nu behöver du göra samma ändring till respektive konfigurationsfil.

I din MEAN.js-lagringsplats, öppnar du `config/env/production.js`.

I objektet `db`, ersätter du värdet `uri` som det visas i följande exempel. Se till att ersätta platshållarna som innan.

```javascript
'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
```

Spara dina ändringar till Git i terminalen. Du kan kopiera bägge kommandona för att köra dem tillsammans.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen, tar du bort alla resurser som skapades i snabbstarten i Azure-portalen med följande steg:

1. I den vänstra menyn i Azure-portalen, klickar du på **resursgrupper** och sedan på namnet på den resurs du skapade. 
2. Klicka på **Ta bort** på sidan med resursgrupper, ange namnet på resursen du vill ta bort i textrutan och klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur man skapar ett Azure Cosmos DB-konto och skapar en MongoDB-samling med datautforskaren. Nu kan du migrera dina MongoDB-data till Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](../documentdb/documentdb-mongodb-migrate.md)

