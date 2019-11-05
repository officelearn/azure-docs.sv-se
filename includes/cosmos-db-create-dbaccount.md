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
ms.openlocfilehash: 93652273dda16e93bdcf9123d7d4a9b3fc956fe9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466180"
---
1. Skapa ett Azure Cosmos DB konto genom att gå till [Azure Portal](https://portal.azure.com/) . Sök efter och välj **Azure Cosmos DB**.

   ![Azure Portal-databasfönstret](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Välj **Lägg till**.
1. På sidan **skapa Azure Cosmos DB konto** anger du grundläggande inställningar för det nya Azure Cosmos-kontot. 

    |Inställning|Värde|Beskrivning |
    |---|---|---|
    |Prenumeration|Prenumerations namn|Välj den Azure-prenumeration som du vill använda för det här Azure Cosmos-kontot. |
    |Resursgrupp|Namn på resursgrupp|Välj en resurs grupp eller Välj **Skapa ny**och ange sedan ett unikt namn för den nya resurs gruppen. |
    |Kontonamn|Ett unikt namn|Ange ett namn för att identifiera ditt Azure Cosmos-konto. Eftersom*documents.azure.com* läggs till det ID du anger för att skapa din URI ska du använda ett unikt ID.<br><br>ID:t får endast innehålla gemener, siffror och bindestreck (-). Det måste vara mellan 3-31 tecken långt.|
    |API|Typ av konto som ska skapas|Välj **Core (SQL)** om du vill skapa en dokument databas och fråga med hjälp av SQL-syntax. <br><br>API:n avgör vilken typ av konto som skapas. Azure Cosmos DB tillhandahåller fem API: er: Core (SQL) och MongoDB för dokument data, Gremlin för graf-data, Azure Table och Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>[Läs mer om SQL-API:et](../articles/cosmos-db/documentdb-introduction.md).|
    |Plats|Den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare för att ge dem snabbast åtkomst till data.|

   ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. Välj **Granska + skapa**. Du kan hoppa över avsnitten **nätverk** och **taggar** .

1. Granska konto inställningarna och välj sedan **skapa**. Det tar några minuter att skapa kontot. Vänta tills Portal sidan visar att **distributionen är klar**. 

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Välj **gå till resurs** för att gå till sidan Azure Cosmos DB konto. 

    ![Sidan Azure Cosmos DB konto](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
