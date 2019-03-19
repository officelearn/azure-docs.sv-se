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
ms.openlocfilehash: 0b9abb80969209fa31f8db6236e57dc817a89f6f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908182"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.
2. I den vänstra menyn klickar du på **skapa en resurs**, klickar du på **databaser**, och välj sedan **Azure Cosmos DB**. 

3. På sidan **Skapa Azure Cosmos DB-konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>Ange sedan samma unika namn som angavs i ID:t|Välj **Skapa ny**. Ange sedan ett nytt resursgruppnamn för kontot. För enkelhetens skull använder du samma namn som för ditt ID. 
    Kontonamn|Ange ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto.<br><br>ID:t får endast innehålla gemener, siffror och bindestreck (-). Det måste vara mellan 3 och 31 tecken långt.
    API|Azure-tabell|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB innehåller fem API:er: Core (SQL) för dokumentdatabaser, Gremlin för grafdatabaser, MongoDB för dokumentdatabaser, Azure Table samt API för Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>Välj **Azure-tabell**, eftersom du i den här snabbstarten skapar en tabell som fungerar med tabell-API. <br><br>[Läs mer om tabell-API](../articles/cosmos-db/table-introduction.md)|
    Plats|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.

4. Du kan lämna **Georedundans** och **flera regioner skriver** alternativ till sina standardvärden som är **inaktivera** att undvika extra RU kostnad. Du kan hoppa över den **nätverk** och **taggar**.

5. Välj **granska + skapa** när verifieringen är klar, Välj **skapa** att skapa kontot. 
 
   ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Skapa konto tar några minuter och du ser **distributionen pågår** meddelande. Vänta tills den har slutförts och välj sedan **gå till resurs**.

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
