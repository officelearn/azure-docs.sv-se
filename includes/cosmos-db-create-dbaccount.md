---
title: ta med fil
description: ta med fil
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/10/2020
ms.custom: include file
ms.openlocfilehash: 2559b7d81a6f60b633d023a35081b54e1204268a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120204"
---
1. Från Azure Portal-menyn eller **Start sidan**väljer du **skapa en resurs**.

1. På sidan **ny** söker du efter och väljer **Azure Cosmos DB**.

1. På sidan **Azure Cosmos DB** väljer du **skapa**.

1. Ange de grundläggande inställningarna för det nya Azure Cosmos-kontot på sidan **Skapa Azure Cosmos DB-konto**. 

    |Inställningen|Värde|Beskrivning |
    |---|---|---|
    |Prenumeration|Prenumerationens namn|Välj den Azure-prenumeration som ska användas för det här Azure Cosmos-kontot. |
    |Resursgrupp|Namn på resursgrupp|Välj en resursgrupp eller välj **Skapa ny** och ange sedan ett unikt namn för den nya resursgruppen. |
    |Kontonamn|Ett unikt namn|Ange ett namn som identifierar ditt Azure Cosmos-konto. Eftersom*documents.azure.com* läggs till det namn du anger för att skapa din URI måste du använda ett unikt namn.<br><br>Namnet får endast innehålla gemener, siffror och bindestreck (-). Det måste innehålla 3–31 tecken.|
    |API|Typ av konto som skapas|Välj **Core (SQL)** för att skapa en dokumentdatabas och kör frågor med hjälp av SQL-syntax. <br><br>API:n avgör vilken typ av konto som skapas. Azure Cosmos DB tillhandahåller fem API: er: Core (SQL) och MongoDB för dokument data, Gremlin för graf-data, Azure Table och Cassandra. För närvarande måste du skapa ett separat konto för varje API. <br><br>[Läs mer om SQL-API: et](../articles/cosmos-db/documentdb-introduction.md).|
    |Använd rabatt på kostnads fri nivå|Tillämpa eller Verkställ inte|Med Azure Cosmos DB kostnads fri nivå får du de första 400 RU/s och 5 GB lagring kostnads fritt i ett konto. Läs mer om den [kostnads fria nivån](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Plats|Den region som är närmast dina användare|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dina användare för att ge dem så snabb åtkomst till data som möjligt.|
    |Kontotyp|Produktion eller icke-produktion|Välj **produktion** om kontot ska användas för en produktions arbets belastning. Välj **icke-produktion** om kontot ska användas för icke-produktion, t. ex. utveckling, testning, frågor och svar eller mellanlagring. Det här är en Azure-resurs tag-inställning som justerar Portal upplevelsen, men som inte påverkar det underliggande Azure Cosmos DB kontot. Du kan ändra det här värdet när som helst.|


    > [!NOTE]
    > Du kan ha upp till en kostnads fri nivå Azure Cosmos DB ett konto per Azure-prenumeration och måste välja när du skapar kontot. Om du inte ser alternativet för att tillämpa rabatten på den kostnads fria nivån innebär det att ett annat konto i prenumerationen redan har Aktiver ATS med den kostnads fria nivån.
   
   ![Den nya kontosidan för Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Välj **Granska + skapa**. Du kan hoppa över avsnitten **Nätverk** och **Taggar**.

1. Granska kontoinställningarna och välj sedan **Skapa**. Det tar några minuter att skapa kontot. Vänta tills portalsidan visar meddelandet **Distributionen är klar**. 

    ![Meddelandefönstret i Azure-portalen](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Välj **Gå till resurs** för att gå till sidan för Azure Cosmos DB-kontot. 

    ![Sidan för Azure Cosmos DB-kontot](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
