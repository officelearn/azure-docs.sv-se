---
title: ta med fil
description: ta med fil
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ecf45a692f83689ed56c03bec13f291781508474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77061730"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.

2. Välj Skapa en **resurs**på den vänstra menyn .
   
   ![Skapa en resurs i Azure-portalen](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. På den **nya** sidan väljer du **Databaser** > **Azure Cosmos DB**.
   
   ![Azure Portal-databasfönstret](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. På sidan **Skapa Azure Cosmos DB-konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>Ange sedan samma namn som kontonamn|Välj **Skapa ny**. Ange sedan ett nytt resursgruppsnamn för ditt konto. För enkelhetens skull kan du använda samma namn som ditt Azure Cosmos DB-kontonamn. 
    Kontonamn|Ange ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto. Ditt konto URI kommer *att läggas mongo.cosmos.azure.com* till ditt unika kontonamn.<br><br>Kontonamnet får bara använda gemener, siffror och bindestreck (-) och måste vara mellan 3 och 31 tecken långa.
    API|Azure Cosmos DB för Mongo DB API|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB innehåller fem API:er: Core (SQL) för dokumentdatabaser, Gremlin för diagramdatabaser, Azure Cosmos DB för Mongo DB API för dokumentdatabaser, Azure Table och Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>Välj **Azure Cosmos DB för Mongo DB API** eftersom du i den här snabbstarten skapar en samling som fungerar med MongoDB.<br><br>[Läs mer om Azure Cosmos DB för MongoDB API](../articles/cosmos-db/mongodb-introduction.md).|
    Location|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.|

    Välj **Granska + skapa**. Du kan hoppa över avsnittet **Nätverk** och **Taggar**. 

    ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Det tar några minuter att skapa kontot. Vänta tills portalen visar **Gratulationerna! Ditt Azure Cosmos DB för Mongo DB API-konto är klar** sida.

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
