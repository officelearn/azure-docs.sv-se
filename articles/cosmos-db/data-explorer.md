---
title: Använda Azure Cosmos DB explorer för att hantera dina data
description: Azure Cosmos DB explorer är ett fristående webbaserat gränssnitt där du kan visa och hantera de data som lagras i Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: dech
ms.openlocfilehash: d50bf7d865cb286f2a1421156b477dc92dc978d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889462"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Arbeta med data med hjälp av Azure Cosmos Explorer 

Azure Cosmos DB explorer är ett fristående webbaserat gränssnitt där du kan visa och hantera de data som lagras i Azure Cosmos DB. Azure Cosmos DB explorer är likvärdiga med den befintliga **Datautforskaren** fliken som är tillgänglig i Azure-portalen när du skapar ett Azure Cosmos DB-konto. Viktiga fördelar jämfört med Azure Cosmos DB explorer befintliga datautforskaren är:

* Du har en fullständig-skärmutrymmet att visa dina data, köra frågor, lagrade procedurer, utlösare och visa resultaten.  

* Du kan ange tillfälligt eller permanent Läs- eller skrivskyddad åtkomst till ditt databaskonto och dess samlingar till andra användare som inte har åtkomst till Azure-portalen eller prenumeration.  

* Du kan dela resultatet av frågan med andra användare som inte har åtkomst till Azure-portalen eller prenumeration.  

## <a name="access-azure-cosmos-db-explorer"></a>Åtkomst till Azure Cosmos DB explorer

1. Logga in på [Azure-portalen](https://portal.azure.com/). 

2. Från **alla resurser**, hitta och navigera till din Azure Cosmos DB-konto, Välj nycklar och kopiera den **primär anslutningssträng**.  

3. Gå till https://cosmos.azure.com/, klistra in anslutningssträngen och väljer **Connect**. Genom att använda anslutningssträngen kan du komma åt Azure Cosmos DB explorer utan några tidsbegränsningar.  

   Om du vill ge andra användare tillfällig åtkomst till ditt Azure Cosmos DB-konto kan göra du det med hjälp av Skriv- och läsbehörighet URL: er. 

4. Öppna den **Datautforskaren** bladet väljer **öppna helskärm**. I popup-dialogrutan kan du visa två åtkomst till URL: er – **skrivskyddad** och **Läs**. Dessa URL: er kan du dela ditt Azure Cosmos DB-konto tillfälligt med andra användare. Åtkomst till kontot upphör att gälla i 24 timmar efter vilken du återansluta genom att använda en ny åtkomst-URL eller anslutningssträngen. 

   **Läs-och** – när du delar Läs-och URL: en med andra användare, de kan visa och ändra databaserna, samlingar, frågor och andra resurser som är associerade med detta konto.

   **Läs** – när du delar den skrivskyddade URL: en med andra användare, de kan visa databaserna, samlingar, frågor och andra resurser som är associerade med detta konto. Om du vill dela resultatet av en fråga med dina gruppmedlemmar som inte har åtkomst till Azure portal eller Azure Cosmos DB-kontot kan du exempelvis ange dem med den här URL: en.

   Välj typ av åtkomst som du vill öppna kontot med och klicka på **öppna**. När du öppnar du Utforskaren är upplevelsen densamma som du hade med fliken Datautforskaren i Azure-portalen.   

   ![Öppna Azure Cosmos DB explorer](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Kända problem

För närvarande den **öppna helskärm** upplevelse som gör det möjligt att dela tillfälliga Skriv- eller läsbehörighet stöds inte ännu för Azure Cosmos DB Gremlin och tabell-API-konton. Du kan fortfarande visa dina Gremlin och tabell-API-konton genom att skicka anslutningssträngen till Azure Cosmos DB Explorer. 

## <a name="next-steps"></a>Nästa steg
Bredvid nu när du har lärt dig hur du kommer igång med Azure Cosmos DB explorer för att hantera dina data, kan du:

* Börja definiera [frågor](sql-api-query-reference.md) med SQL-syntax och utföra [server sida programmering](stored-procedures-triggers-udfs.md) genom att använda lagrade procedurer, UDF: er, utlöser. 
