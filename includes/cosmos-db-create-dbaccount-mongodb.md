---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 690c5f07a2b0f7a88e16f0b0bbbaa9ca78e37317
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38729008"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt fönster.
2. Klicka på **Skapa en resurs** i menyn till vänster. Klicka på **Databaser** och sedan på **Skapa** under **Azure Cosmos DB**.
   
   ![Skärmbild av Azure Portal med fokus på Fler tjänster och Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Ange **MongoDB** som API och fyll i önskad konfiguration för Azure Cosmos DB-kontot på bladet **Nytt konto**.
 
    * **ID** måste vara ett unikt namn du väljer för att identifiera ditt Azure Cosmos DB-konto. Det får bara innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken.
    * **Prenumeration** är din Azure-prenumeration. Det fylls i automatiskt.
    * **Resursgrupp** är resursgruppsnamnet för ditt Azure Cosmos DB-konto. Välj **Skapa ny** och ange sedan ett nytt resursgruppsnamn för ditt konto. För enkelhetens skull kan du använda samma namn som för ditt ID.
    * **Plats** är den geografiska plats där din instans av Azure Cosmos DB finns. Välj den plats som är närmast dina användare.

    Klicka sedan på **Skapa**.

    ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Det tar några minuter att skapa kontot. Vänta tills portalen visar sidan **Grattis! Azure Cosmos DB-kontot med MongoDB API är klart.**

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
