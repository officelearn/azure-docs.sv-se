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
ms.openlocfilehash: 1f36f2f26b65fc6078b99a43b6f0bae9a8a8e9f5
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578399"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.

2. Välj **skapa en resurs**i den vänstra menyn.
   
   ![Skapa en resurs på Azure-portalen](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-0.png)
   
3. Välj **databaser**Azure Cosmos DB på sidan **nytt**  >  **Azure Cosmos DB**.
   
   ![Fönstret Databaser på Azure Portal](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)
   
   > [!TIP]
   > Om du inte ser Azure Cosmos DB i listan skriver du den i rutan Sök högst upp på sidan och trycker på _RETUR_ . 

3. På sidan **skapa Azure Cosmos DB konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>Ange sedan samma namn som konto namnet|Välj **Skapa ny**. Ange sedan ett nytt resurs grupps namn för ditt konto. För enkelhetens skull använder du samma namn som ditt Azure Cosmos DB konto namn. 
    Account Name|Ange ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto. Din konto-URI kommer att *Gremlin.Azure.com* till ditt unika konto namn.<br><br>Konto namnet får bara innehålla gemena bokstäver, siffror och bindestreck (-) och måste vara mellan 3 och 31 tecken långt.
    API|Gremlin (graf)|API:et avgör vilken typ av konto som skapas. Azure Cosmos DB tillhandahåller fem API: er: Core (SQL) för dokument databaser, Gremlin för graf-databaser, MongoDB för dokument databaser, Azure Table och Cassandra. Du måste skapa ett separat konto för varje API. <br><br>Välj **Gremlin (graf)**, eftersom du i den här snabb starten skapar en tabell som fungerar med Gremlin-API: et. <br><br>[Läs mer om Gremlin-API: et](../articles/cosmos-db/graph-introduction.md).|
    Plats|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.

    Välj **Granska + skapa**. Du kan hoppa över avsnittet **Nätverk** och **Taggar**. 

    ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. Det tar några minuter att skapa kontot. Vänta tills portalen visar **Grattis! Ditt Azure Cosmos DB konto har skapats** .
   
   ![Sidan Azure Cosmos DB konto har skapats](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)

