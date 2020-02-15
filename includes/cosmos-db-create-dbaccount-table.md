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
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212762"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.

2. Välj **skapa en resurs**i den vänstra menyn.
   
   ![Skapa en resurs i Azure Portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png)
   
3. Välj **databaser** > **Azure Cosmos DB**på sidan **nytt** .
   
   ![Azure Portal-databasfönstret](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)
   
3. På sidan **skapa Azure Cosmos DB konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|**Skapa nytt**och sedan konto namn|Välj **Skapa ny**. Ange sedan ett nytt resurs grupps namn för ditt konto. För enkelhetens skull använder du samma namn som ditt Azure Cosmos DB konto namn. 
    Kontonamn|Ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto.<br><br>Konto namnet får bara innehålla gemena bokstäver, siffror och bindestreck (-) och måste vara mellan 3 och 31 tecken långt.
    API|Tabell|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB tillhandahåller fem API: er: Core (SQL) för dokument databaser, Gremlin för graf-databaser, MongoDB för dokument databaser, Azure Table och Cassandra. Du måste skapa ett separat konto för varje API. <br><br>Välj **Azure-tabell**, eftersom du i den här snabb starten skapar en tabell som fungerar med tabell-API. <br><br>[Läs mer om tabell-API](../articles/cosmos-db/table-introduction.md).|
    plats.|Den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.

    Du kan lämna alternativen **GEO-redundans** och **flera regioner** vid **inaktive ring** för att undvika ytterligare kostnader och hoppa över avsnitten **nätverk** och **taggar** .

5. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa** för att skapa kontot. 
 
   ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Det tar några minuter att skapa kontot. Ett meddelande visas som anger att **distributionen pågår**. Vänta tills distributionen är klar och välj sedan **gå till resurs**.

    ![Fönstret Azure Portal meddelanden](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)

