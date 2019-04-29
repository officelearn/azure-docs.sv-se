---
title: ta med fil
description: ta med fil
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 5d57d7e18befba175a5a8a825494ce512644b5a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553080"
---
1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Välj **Skapa en resurs** > **Databaser** > **Azure Cosmos DB**.
   
   ![Azure Portal-databasfönstret](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. På den **skapar Azure Cosmos DB-konto** anger de grundläggande inställningarna för det nya Azure Cosmos-kontot. 
 
    |Inställning|Värde|Beskrivning |
    |---|---|---|
    |Prenumeration|Prenumerationsnamn|Välj den prenumeration som du vill använda för det här Azure Cosmos-kontot. |
    |Resursgrupp|Namn på resursgrupp|Välj en resursgrupp eller välj **Skapa nytt**, ange ett unikt namn för den nya resursgruppen. |
    | Kontonamn|Ange ett unikt namn|Ange ett namn som identifierar ditt Azure Cosmos-konto. Eftersom*documents.azure.com* läggs till det ID du anger för att skapa din URI ska du använda ett unikt ID.<br><br>ID:t får endast innehålla gemener, siffror och bindestreck (-). Det måste vara mellan 3-31 tecken.|
    | API|Core (SQL)|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB innehåller fem API:er: Core (SQL) och MongoDB för dokumentdata, Gremlin för diagramdata, Azure Table och Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>Välj **Core (SQL)** att skapa en dokumentdatabas och en fråga med hjälp av SQL-syntax. <br><br>[Läs mer om SQL-API:et](../articles/cosmos-db/documentdb-introduction.md).|
    | Location|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare att ge dem snabbast åtkomst till data.|
   
   ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

1. Välj **Granska + skapa**. Du kan hoppa över den **nätverk** och **taggar** avsnitt. 

1. Granska inställningarna för kontot och välj sedan **skapa**. Det tar några minuter att skapa kontot. Vänta tills portalsidan att visa **distributionen är klar**. 

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Välj **gå till resurs** att gå till sidan för Azure Cosmos DB-konto. 

    ![Sidan för Azure Cosmos DB-konto](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
