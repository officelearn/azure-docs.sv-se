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
ms.openlocfilehash: adff3c4096a1464913997489cb50665a450024b6
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53286101"
---
1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.
2. Klicka på **Skapa en resurs** i menyn till vänster. Klicka på **Databaser** och sedan på **Skapa** under **Azure Cosmos DB**. 
   
   ![Skärmbild av Azure Portal med fokus på Fler tjänster och Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. På sidan **Skapa Azure Cosmos DB-konto** anger du inställningarna för det nya Azure Cosmos DB-kontot. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|Din prenumeration|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos DB-kontot. 
    Resursgrupp|Skapa ny<br><br>Ange sedan samma unika namn som angavs i ID:t|Välj **Skapa ny**. Ange sedan ett nytt resursgruppnamn för kontot. För enkelhetens skull använder du samma namn som för ditt ID. 
    Kontonamn|Ange ett unikt namn|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto. Eftersom*documents.azure.com* läggs till det ID du anger för att skapa din URI ska du använda ett unikt ID.<br><br>ID:t får endast innehålla gemener, siffror och bindestreck (-). Det måste vara mellan 3 och 31 tecken långt.
    API|Azure-tabell|API:n avgör vilken typ av konto som skapas. Azure Cosmos DB innehåller fem API:er: Core (SQL) för dokumentdatabaser, Gremlin för grafdatabaser, MongoDB för dokumentdatabaser, Azure Table samt API för Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>Välj **Azure-tabell**, eftersom du i den här snabbstarten skapar en tabell som fungerar med tabell-API. <br><br>[Läs mer om tabell-API](../articles/cosmos-db/table-introduction.md)|
    Plats|Välj den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare så att de får så snabb åtkomst till data som möjligt.

    Välj **granska + skapa**. Du kan hoppa över avsnittet **Nätverk** och **Taggar**.  

    ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

4. Det tar några minuter att skapa kontot. Vänta tills portalen visar sidan **Grattis! Azure Cosmos DB-kontot har skapats**.

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
