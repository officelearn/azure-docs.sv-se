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
ms.openlocfilehash: 4fec9be34e390498b85ecfcb3f3b61055a08fdd2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681315"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.
2. I det vänstra navigeringsfönstret väljer **skapa en resurs**. Välj **databaser** och välj sedan **Azure Cosmos DB**.
   
   ![Skärmbild av Azure-portalen som visar fler tjänster och Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. På den **skapar Azure Cosmos DB-konto** anger inställningarna för det nya Azure Cosmos DB-kontot:
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>Ange sedan samma unika namn som angavs i ID:t|Välj **Skapa ny**. Ange sedan ett nytt Resursgruppsnamn för ditt konto. För enkelhetens skull använder du samma namn som för ditt ID. 
    Kontonamn|Ange ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto.<br><br>ID:t får endast innehålla gemener, siffror och bindestreck (-). Det måste vara mellan 3 och 31 tecken långt.
    API|Azure-tabell|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB innehåller fem API:er: Core (SQL) för dokumentdatabaser, Gremlin för grafdatabaser, MongoDB för dokumentdatabaser, Azure Table samt API för Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>Välj **Azure Table** eftersom i den här snabbstarten skapar du en tabell som fungerar med tabell-API. <br><br>[Läs mer om tabell-API](../articles/cosmos-db/table-introduction.md).|
    Location|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare att ge dem snabbast åtkomst till data.

    Du kan lämna den **Georedundans** och **flera regioner skriver** alternativ på standardvärdena (**inaktivera**) att undvika ytterligare RU-avgifter. Du kan hoppa över den **nätverk** och **taggar** avsnitt.

5. Välj **Granska + skapa**. När verifieringen är klar, Välj **skapa** att skapa kontot. 
 
   ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Det tar några minuter att skapa kontot. Du ser ett meddelande om **distributionen pågår**. Vänta tills distributionen är klar och välj sedan **gå till resurs**.

    ![Fönstret meddelanden i Azure portal](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
