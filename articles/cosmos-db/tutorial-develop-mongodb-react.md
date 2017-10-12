---
title: "Självstudiekurs om MongoDB, React och Node.js för Azure | Microsoft Docs"
description: "Lär dig hur du skapar en MongoDB-app med React och Node.js i Azure Cosmos DB med hjälp av exakt samma API:er som du använder för MongoDB i den här videobaserade självstudieserien."
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
ms.custom: mvc
ms.openlocfilehash: 622a26da8b7d5f5c3a242c5c88a4ae479095bf6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>Skapa en MongoDB-app med React och Azure Cosmos DB  

Den här videobaserade självstudiekursen i flera dela demonstrerar hur du skapar en hero-spårningsapp med en React-klient. Appen använder Node och Express för servern, ansluter till Azure Cosmos DB med [MongoDB-API:et](mongodb-introduction.md) och ansluter sedan React-klienten till serverdelen av appen. I den här självstudiekursen lär du dig också hur du skalar Azure Cosmos DB på Azure-portalen genom att bara peka och klicka samt hur du distribuerar appen till Internet så att alla kan spåra sina heroes-favoritkomponenter. 

Eftersom [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stöder MongoDB-klientanslutningar kan du använda Azure Cosmos DB i stället för MongoDB, men använda samma kod som du använder för MongoDB-appar, men med ytterligare fördelar som enkel molndistribution, skalning och blixtsnabba läsningar och skrivningar.  

Den här självstudiekursen i flera delar består av följande uppgifter:

> [!div class="checklist"]
> * Introduktion
> * Konfigurera projektet
> * Skapa användargränssnittet med React
> * Skapa ett Azure Cosmos DB-konto med hjälp av Azure-portalen
> * Ansluta till Azure Cosmos DB med hjälp av Mongoose
> * Lägga till React-, Create-, Update- och Delete-åtgärder till appen

Vill du skapa den här appen med Angular? Gå [den videobaserade självstudieserien för Angular](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Krav
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Det färdiga projektet
Hämta det färdiga programmet [från GitHub](https://github.com/Azure-Samples/react-cosmosdb).

## <a name="introduction"></a>Introduktion 

I den här videon ger Burke Holland en introduktion av Azure Cosmos DB och går igenom appen som skapas i den här videoserien. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Projektkonfiguration

Den här videon visar hur du konfigurerar Express och React i samma projekt. Därefter går Burke igenom koden i projektet.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Skapa användargränssnittet

Den här videon visar hur du skapar programmets användargränssnitt (UI) med React. 

> [!NOTE]
> CSS-koden som vi refererar till i den här videon finns i [GitHub-databasen react-cosmosdb](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css).

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Ansluta till Azure Cosmos DB

Den här videon visar hur du skapar ett nytt Azure Cosmos DB-konto på Azure-portalen och hur du installerar MongoDB- och Mongoose-paketen och sedan ansluter appen till det nya kontot med hjälp av Azure Cosmos DB-anslutningssträngen. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Läsa och skapa heroes-komponenter i appen

Den här videon visar hur du läser heroes-komponenter och skapar heroes-komponenter i Cosmos DB-databasen, samt hur du testar dessa metoder med hjälp av Postman och React-användargränssnittet. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Ta bort och uppdatera heroes-komponenter i appen

Den här videon visar hur du tar bort och uppdaterar heroes-komponenter från appen och hur du visar uppdateringarna i användargränssnittet. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Slutföra appen

Den här videon visar hur du slutför appen och hur du kopplar användargränssnittet till API:et för serverdelen. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda den här appen mer följer du stegen nedan för att ta bort alla resurser som du har skapat i den här självstudiekursen på Azure-portalen. 

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du lärt dig att:

> [!div class="checklist"]
> * Skapa en app med React, Node, Express och Azure Cosmos DB 
> * Skapa ett Azure Cosmos DB-konto
> * Ansluta appen till Azure Cosmos DB-kontot
> * Testa appen med hjälp av Postman
> * Köra programmet och lägga till heroes-komponenter i databasen

Håll utkik efter en till video i den här självstudieserien som visar hur du distribuerar programmet och hur du replikerar dina data globalt.

Fortsätt med nästa självstudiekurs och lär dig hur du importerar MongoDB-data till Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](mongodb-migrate.md)
 
