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
ms.openlocfilehash: d6eeb62ce990c24138f14e7f3b8a1ce4048e0174
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50746803"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.
2. Klicka på **Skapa en resurs** > **Databaser** > **Azure Cosmos DB**.
   
   ![Azure Portal-databasfönstret](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. På sidan **Nytt konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    ID|*Ange ett unikt namn*|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto. Eftersom*documents.azure.com* läggs till det ID du anger för att skapa din URI ska du använda ett unikt ID.<br><br>Ditt ID får bara innehålla gemener, siffror och bindestreck och måste innehålla mellan 3 och 50 tecken.
    API|SQL|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB innehåller fem API:er: SQL (dokumentdatabas), Gremlin (grafdatabas), MongoDB (dokumentdatabas), Tabell-API och API för Cassandra. Varje API kräver för närvarande att du skapar ett separat konto. <br><br>Välj **SQL** eftersom du i den här artikeln skapar en dokumentdatabas och fråga med hjälp av SQL-syntax. <br><br>[Läs mer om SQL-API:et](../articles/cosmos-db/documentdb-introduction.md)|
    Prenumeration|*Din prenumeration*|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>*Ange sedan samma unika namn som angavs ovan i ID:t*|Välj **Skapa ny** och ange sedan ett nytt resursgruppsnamn för ditt konto. För enkelhetens skull kan du använda samma namn som för ditt ID. 
    Plats|*Välj den region som är närmast dina användare*|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.
    Aktivera geo-redundans| Lämna tomt | Detta skapar en replikerad version av databasen i en andra (parad) region. Låt den vara tom.  

    Klicka sedan på **Skapa**.

    ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. Det tar några minuter att skapa kontot. Vänta tills portalen visar sidan **Grattis! Azure Cosmos DB-kontot har skapats**.

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

