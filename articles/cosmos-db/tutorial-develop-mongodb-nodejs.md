---
title: "Självstudiekurs om MongoDB, Angular och Node för Azure | Microsoft Docs"
description: "Lär dig hur du skapar en MongoDB-app med Angular och Node i Azure Cosmos DB med hjälp av exakt samma API:er som du använder för MongoDB i den här videobaserade självstudieserien."
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
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 37bc34c470190f3acb1f0f2e9bed5e0a4918405b
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db"></a>Skapa en MongoDB-app med Angular och Azure Cosmos DB 

Den här självstudiekursen i flera delar demonstrerar hur du skapar en ny [MongoDB API](mongodb-introduction.md)-app med Express, Angular och Node.js (MEAN-stacken) och ansluter den till din Azure Cosmos DB-databas. Eftersom Azure Cosmos DB stöder MongoDB-klientanslutningar kan du använda Azure Cosmos DB i stället för MongoDB, men använda samma kod som du använder för MongoDB-appar, men med ytterligare fördelar. Exempel på sådana fördelar i Azure Cosmos DB är enkel molndistribution, skalning, säkerhet, globalt replikerade data, stöd för flera modeller och blixtsnabba läsningar och skrivningar. 

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Med den här tjänsten kan du snabbt skapa och köra frågor mot databaser med dokument, nyckel/värde-par och grafer som drar nytta av fundamentala funktioner i Azure Cosmos DB som global distribution och horisontell skalning. 

Den här självstudiekursen i flera delar består av följande uppgifter:

> [!div class="checklist"]
> * [Skapa en Node.js-baserad Express-app med Angular CLI](tutorial-develop-mongodb-nodejs-part2.md)
> * [Skapa användargränssnittet med Angular](tutorial-develop-mongodb-nodejs-part3.md)
> * [Skapa ett Azure Cosmos DB-konto med hjälp av Azure CLI](tutorial-develop-mongodb-nodejs-part4.md) 
> * [Ansluta till Azure Cosmos DB med hjälp av Mongoose](tutorial-develop-mongodb-nodejs-part5.md)
> * [Lägga till Post-, Put- och Delete-funktioner till appen](tutorial-develop-mongodb-nodejs-part6.md)

Vill du skapa den här appen med React? Gå [den videobaserade självstudieserien om React](tutorial-develop-mongodb-react.md).

## <a name="video-walkthrough"></a>Videogenomgång

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>Det färdiga projektet 

Den här självstudiekursen beskriver steg för steg hur du skapar programmet. Om du vill hämta det färdiga projektet kan du ladda ned programmet från [angular-cosmosdb-databasen](https://github.com/Azure-Samples/angular-cosmosdb) på GitHub.

## <a name="next-steps"></a>Nästa steg

I den här delen av självstudiekursen har du gjort följande:

> [!div class="checklist"]
> * Fått en översikt över de steg du följer för att skapa en MEAN.js-app med Azure Cosmos DB. 

Fortsätt till nästa del av självstudiekursen och skapa den Node.js-baserade Express-appen.

> [!div class="nextstepaction"]
> [Skapa en Node.js-baserad Express-app med Angular CLI](tutorial-develop-mongodb-nodejs-part2.md)
