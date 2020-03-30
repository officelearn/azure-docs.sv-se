---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/27/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a6cfafd41d1632d99b7ca03bce2316ec679d7579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061862"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.

2. Välj Skapa en **resurs**på den vänstra menyn .
   
   ![Skapa en resurs i Azure-portalen](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-0.png)
   
3. På den **nya** sidan väljer du **Databaser** > **Azure Cosmos DB**.
   
   ![Azure Portal-databasfönstret](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)
   
3. På sidan **Skapa Azure Cosmos DB-konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>Ange sedan samma namn som kontonamn|Välj **Skapa ny**. Ange sedan ett nytt resursgruppsnamn för ditt konto. För enkelhetens skull kan du använda samma namn som ditt Azure Cosmos DB-kontonamn. 
    Kontonamn|Ange ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto. Ditt konto URI kommer *att läggas gremlin.azure.com* till ditt unika kontonamn.<br><br>Kontonamnet får bara använda gemener, siffror och bindestreck (-) och måste vara mellan 3 och 31 tecken långa.
    API|Gremlin (graf)|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB innehåller fem API:er: Core (SQL) för dokumentdatabaser, Gremlin för diagramdatabaser, MongoDB för dokumentdatabaser, Azure Table och Cassandra. Du måste skapa ett separat konto för varje API. <br><br>Välj **Gremlin (diagram)** eftersom du i den här snabbstarten skapar en tabell som fungerar med Gremlin-API:et. <br><br>[Läs mer om Gremlin-API:et](../articles/cosmos-db/graph-introduction.md).|
    Location|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.

    Välj **Granska + skapa**. Du kan hoppa över avsnittet **Nätverk** och **Taggar**. 

    ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. Det tar några minuter att skapa kontot. Vänta tills portalen visar **Gratulationerna! Din Azure Cosmos DB-konto skapades** sidan.
   
   ![Skapad sida för Azure Cosmos DB-konto](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)

