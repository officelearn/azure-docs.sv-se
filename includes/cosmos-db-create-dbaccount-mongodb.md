---
title: ta med fil
description: ta med fil
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: b1dcf395c55e91a98b237f6e3866c97d74b7a97c
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942801"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt fönster.
2. På den vänstra menyn väljer du **skapa en resurs**, väljer **databaser**och väljer sedan **skapa**under **Azure Cosmos DB**.
   
   ![Skärm bild av Azure Portal, markerar fler tjänster och Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. På sidan **Skapa Azure Cosmos DB-konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>Ange sedan samma unika namn som angavs i ID:t|Välj **Skapa ny**. Ange sedan ett nytt resursgruppnamn för kontot. För enkelhetens skull använder du samma namn som för ditt ID. 
    Kontonamn|Ange ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto. Eftersom *Mongo.Cosmos.Azure.com* läggs till det ID som du anger för att skapa din URI, använder du ett unikt ID.<br><br>ID:t får endast innehålla gemener, siffror och bindestreck (-). Det måste vara mellan 3 och 31 tecken långt.
    API|API för Azure Cosmos DB för MongoDB|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB tillhandahåller fem API: er: Core (SQL) för dokument databaser, Gremlin för graf-databaser, Azure Cosmos DBs API-MongoDB för dokument databaser, Azure Table och Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>Välj **MongoDB** eftersom du skapar en samling som fungerar med MongoDB i den här snabb starten.|
    Location|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.
    Version|3.6|Välj MongoDB Wire Protocol version 3,6 eller för bakåtkompatibilitet, Välj 3,2.

    Välj **Granska + skapa**. Du kan hoppa över avsnittet **Nätverk** och **Taggar**. 

    ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Det tar några minuter att skapa kontot. Vänta tills portalen visar **Grattis! Ditt Cosmos-konto med Wire Protocol-kompatibilitet för MongoDB är klart** .

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
