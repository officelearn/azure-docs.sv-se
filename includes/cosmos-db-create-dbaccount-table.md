---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 2001d0621a340cbdb04c0bb5eea1166ce8b88eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212762"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.

2. Välj Skapa en **resurs**på den vänstra menyn .
   
   ![Skapa en resurs i Azure-portalen](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png)
   
3. På den **nya** sidan väljer du **Databaser** > **Azure Cosmos DB**.
   
   ![Azure Portal-databasfönstret](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)
   
3. På sidan **Skapa Azure Cosmos DB-konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|**Skapa nytt**och sedan Kontonamn|Välj **Skapa ny**. Ange sedan ett nytt resursgruppsnamn för ditt konto. För enkelhetens skull kan du använda samma namn som ditt Azure Cosmos DB-kontonamn. 
    Kontonamn|Ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto.<br><br>Kontonamnet får bara använda gemener, siffror och bindestreck (-) och måste vara mellan 3 och 31 tecken långa.
    API|Tabell|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB innehåller fem API:er: Core (SQL) för dokumentdatabaser, Gremlin för diagramdatabaser, MongoDB för dokumentdatabaser, Azure Table och Cassandra. Du måste skapa ett separat konto för varje API. <br><br>Välj **Azure-tabell**, eftersom du i den här snabbstarten skapar en tabell som fungerar med tabell-API:et. <br><br>[Läs mer om tabell-API:et](../articles/cosmos-db/table-introduction.md).|
    Location|Den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.

    Du kan lämna alternativen **Geo-Redundans** och **Flera regioner** på **Inaktivera** för att undvika ytterligare avgifter och hoppa över avsnitten **Nätverk** och **taggar.**

5. Välj **Granska + skapa**. När valideringen är klar väljer du **Skapa** för att skapa kontot. 
 
   ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Det tar några minuter att skapa kontot. Ett meddelande om att **distributionen är på gång**visas. Vänta tills distributionen är klar och välj sedan **Gå till resurs**.

    ![Fönstret Azure-portalmeddelanden](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)

