---
title: Skapa en Angular-app med Azure Cosmos DB:s API för MongoDB – skapa ett Cosmos-konto
titleSuffix: Azure Cosmos DB
description: Del 4 i självstudieserien om hur du skapar en MongoDB-app med Angular och Node i Azure Cosmos DB med exakt samma API:er som du använder för MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.openlocfilehash: d85183f0104d9066f93adc73c09c76406128700d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53787808"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Skapa en Angular-app med Azure Cosmos DB:s API för MongoDB – skapa ett Cosmos-konto

Den här självstudien i flera delar visar hur du skapar en ny app skriven i Node.js med Express och Angular och sedan ansluter den till ditt [Cosmos-konto konfigurerat med Cosmos DB:s API för MongoDB](mongodb-introduction.md).

Del 4 av självstudiekursen bygger vidare på [del 3](tutorial-develop-mongodb-nodejs-part3.md) och består av följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure-resursgrupp med hjälp av Azure CLI
> * Skapa ett Cosmos-konto med hjälp av Azure CLI

## <a name="video-walkthrough"></a>Videogenomgång

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Nödvändiga komponenter

Utför stegen i [del 3](tutorial-develop-mongodb-nodejs-part3.md) av självstudiekursen innan du påbörjar den här delen. 

I den här delen av självstudiekursen kan du antingen använda Azure Cloud Shell (i webbläsaren) eller en lokal installation av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Den här självstudiekursen beskriver steg för steg hur du skapar programmet. Om du vill hämta det färdiga projektet kan du ladda ned programmet från [angular-cosmosdb-databasen](https://github.com/Azure-Samples/angular-cosmosdb) på GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Skapa ett Azure Cosmos DB-konto med kommandot [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create).

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Använd ditt unika Azure Cosmos DB-kontonamn för `<cosmosdb-name>`. Namnet måste vara unikt bland alla Azure Cosmos DB-kontonamn i Azure.
* Inställningen `--kind MongoDB` gör att Azure Cosmos DB kan använda MongoDB-klientanslutningar.

Det kan ta ett par minuter innan kommandot har slutförts. När det är klart visas information om den nya databasen i terminalfönstret. 

När Azure Cosmos DB-kontot har skapats:
1. Öppna ett nytt webbläsarfönster och gå till [https://portal.azure.com](https://portal.azure.com)
1. Klicka på Azure Cosmos DB-logotypen ![Azure Cosmos DB-ikon på Azure-portalen](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) i det vänstra fältet så ser du alla dina Azure Cosmos DB-databaser.
1. Klicka på det Azure Cosmos DB-konto som du precis skapat, välj fliken **Översikt** och rulla nedåt tills du ser kartan som visar databasens plats. 

    ![Nytt Azure Cosmos DB-konto på Azure-portalen](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. Rulla nedåt i det vänstra navigeringsfönstret och klicka på fliken **Replikera data globalt**. En karta visas där du ser de olika regioner som du kan replikera till. Du kan till exempel klicka på Sydöstra Australien eller Östra Australien om du vill replikera dina data till Australien. Mer information om global replikering finns i avsnittet [How to distribute data globally with Azure Cosmos DB](distribute-data-globally.md) (Distribuera data globalt med Azure Cosmos DB). För stunden nöjer vi oss med vår instans, men när vi vill replikera vet vi hur vi gör.

    ![Nytt Azure Cosmos DB-konto på Azure-portalen](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>Nästa steg

I den här delen av självstudiekursen har du gjort följande:

> [!div class="checklist"]
> * Skapat en Azure-resursgrupp med hjälp av Azure CLI
> * Skapat ett Azure Cosmos DB-konto med hjälp av Azure CLI

Fortsätt till nästa del av självstudiekursen och anslut Azure Cosmos DB till din app med hjälp av Mongoose.

> [!div class="nextstepaction"]
> [Ansluta till Azure Cosmos DB med hjälp av Mongoose](tutorial-develop-mongodb-nodejs-part5.md)
