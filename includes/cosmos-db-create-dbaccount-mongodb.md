---
title: ta med fil
description: ta med fil
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ecf45a692f83689ed56c03bec13f291781508474
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061730"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.

2. Välj **skapa en resurs**i den vänstra menyn.
   
   ![Skapa en resurs i Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Välj **databaser** > **Azure Cosmos DB**på sidan **nytt** .
   
   ![Azure Portal-databasfönstret](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. På sidan **skapa Azure Cosmos DB konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>Ange sedan samma namn som konto namnet|Välj **Skapa ny**. Ange sedan ett nytt resurs grupps namn för ditt konto. För enkelhetens skull använder du samma namn som ditt Azure Cosmos DB konto namn. 
    Kontonamn|Ange ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto. Din konto-URI kommer att *Mongo.Cosmos.Azure.com* till ditt unika konto namn.<br><br>Konto namnet får bara innehålla gemena bokstäver, siffror och bindestreck (-) och måste vara mellan 3 och 31 tecken långt.
    API|Azure Cosmos DB för mongo DB-API|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB tillhandahåller fem API: er: Core (SQL) för dokument databaser, Gremlin för graf-databaser, Azure Cosmos DB för mongo DB API för dokument databaser, Azure Table och Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>Välj **Azure Cosmos dB för MONGO DB-API** eftersom du skapar en samling som fungerar med MongoDB i den här snabb starten.<br><br>[Läs mer om Azure Cosmos dB för MongoDB-API: et](../articles/cosmos-db/mongodb-introduction.md).|
    plats.|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.|

    Välj **Granska + skapa**. Du kan hoppa över avsnittet **Nätverk** och **Taggar**. 

    ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Det tar några minuter att skapa kontot. Vänta tills portalen visar **Grattis! Din Azure Cosmos DB för mongo DB API-konto är klar** .

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
