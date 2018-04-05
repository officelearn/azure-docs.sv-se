---
title: Skapa ett Azure Cosmos DB MongoDB API-konto
description: Beskriver hur du skapar ett Azure Cosmos DB MongoDB API-konto med hjälp av Azure Portal
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt fönster.
2. Klicka på **Skapa en resurs** i menyn till vänster. Klicka på **Databaser** och sedan på **Skapa** under **Azure Cosmos DB**.
   
   ![Skärmbild av Azure Portal med fokus på Fler tjänster och Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Ange **MongoDB** som API och fyll i önskad konfiguration för Azure Cosmos DB-kontot på bladet **Nytt konto**.
 
    ![Skärmbild av bladet Ny Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * **ID** måste vara ett unikt namn du väljer för att identifiera ditt Azure Cosmos DB-konto. Det får bara innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken.
    * **Prenumeration** är din Azure-prenumeration. Det fylls i automatiskt.
    * **Resursgrupp** är resursgruppsnamnet för ditt Azure Cosmos DB-konto.
    * **Plats** är den geografiska plats där din instans av Azure Cosmos DB finns. Välj den plats som är närmast dina användare.

4. Skapa kontot genom att klicka på **Skapa**.
5. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

    ![Meddelande om att distributionen har påbörjats](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  När distributionen är färdig öppnar du det nya kontot från panelen Alla resurser. 

    ![Azure Cosmos DB-konto i panelen Alla resurser](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
