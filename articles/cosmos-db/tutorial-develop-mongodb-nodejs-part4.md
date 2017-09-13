---
title: "Självstudiekurs om MongoDB, Angular och Node för Azure – del 4 | Microsoft Docs"
description: "Del 4 i självstudieserien om hur du skapar en MongoDB-app med Angular och Node i Azure Cosmos DB med exakt samma API:er som du använder för MongoDB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: 6fd88c58c0067deab08d502be4bd624c297b5fde
ms.contentlocale: sv-se
ms.lasthandoff: 09/06/2017

---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-4-create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Skapa en MongoDB-app med Angular och Azure Cosmos DB – del 4: Skapa ett Azure Cosmos DB-konto med hjälp av Azure CLI

Den här självstudiekursen i flera delar demonstrerar hur du skapar en ny [MongoDB API](mongodb-introduction.md)-app skriven i Node.js med Express, Angular och din Azure Cosmos DB-databas.

Del 4 av självstudiekursen bygger vidare på [del 3](tutorial-develop-mongodb-nodejs-part3.md) och består av följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure-resursgrupp med hjälp av Azure CLI
> * Skapa ett Azure Cosmos DB-konto med hjälp av Azure CLI

## <a name="video-walkthrough"></a>Videogenomgång

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Krav

Utför stegen i [del 3](tutorial-develop-mongodb-nodejs-part3.md) av självstudiekursen innan du påbörjar den här delen. 

I den här delen av självstudiekursen kan du antingen använda Azure Cloud Shell (i webbläsaren) eller en lokal installation av [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Om du använder Azure CLI lokalt kontrollerar du att du har Azure CLI version 2.0 eller senare. Du kan kontrollera din version genom att köra `az --version` i kommandotolken. 

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Den här självstudiekursen beskriver steg för steg hur du skapar programmet. Om du vill hämta det färdiga projektet kan du ladda ned programmet från [angular-cosmosdb-databasen](https://github.com/Azure-Samples/angular-cosmosdb) på GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Skapa ett Azure Cosmos DB-konto med kommandot [`az cosmosdb create`](/cli/azure/cosmosdb#create).

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

