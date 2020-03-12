---
title: Använda Azure Cosmos DB explorer för att hantera dina data
description: Azure Cosmos DB explorer är ett fristående webbaserat gränssnitt där du kan visa och hantera de data som lagras i Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096812"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Arbeta med data med hjälp av Azure Cosmos Explorer 

Azure Cosmos DB explorer är ett fristående webbaserat gränssnitt där du kan visa och hantera de data som lagras i Azure Cosmos DB. Azure Cosmos DB Explorer motsvarar fliken befintlig **datautforskaren** som är tillgänglig i Azure Portal när du skapar ett Azure Cosmos DB-konto. Viktiga fördelar jämfört med Azure Cosmos DB explorer befintliga datautforskaren är:

* Du har en fullständig-skärmutrymmet att visa dina data, köra frågor, lagrade procedurer, utlösare och visa resultaten.  

* Du kan ange tillfälligt eller permanent Läs- eller skrivskyddad åtkomst till ditt databaskonto och dess samlingar till andra användare som inte har åtkomst till Azure-portalen eller prenumeration.  

* Du kan dela resultatet av frågan med andra användare som inte har åtkomst till Azure-portalen eller prenumeration.  

## <a name="access-azure-cosmos-db-explorer"></a>Åtkomst till Azure Cosmos DB explorer

1. Logga in på [Azure-portalen](https://portal.azure.com/). 

2. Från **alla resurser**, leta upp och navigera till ditt Azure Cosmos DB konto, Välj nycklar och kopiera den **primära anslutnings strängen**.  

3. Gå till https://cosmos.azure.com/, klistra in anslutnings strängen och välj **Anslut**. Genom att använda anslutningssträngen kan du komma åt Azure Cosmos DB explorer utan några tidsbegränsningar.  

   Om du vill ge andra användare tillfällig åtkomst till ditt Azure Cosmos DB-konto kan göra du det med hjälp av Skriv- och läsbehörighet URL: er. 

4. Öppna bladet **datautforskaren** och välj **Öppna hel skärms läge**. I popup-dialogrutan kan du Visa två åtkomst webb adresser – **Läs-och skriv-** och **Läs**behörighet. Dessa URL: er kan du dela ditt Azure Cosmos DB-konto tillfälligt med andra användare. Åtkomst till kontot upphör att gälla i 24 timmar efter vilken du återansluta genom att använda en ny åtkomst-URL eller anslutningssträngen. 

   **Read-Write** – när du delar URL: en med Läs-och Skriv behörighet till andra användare kan de Visa och ändra databaser, samlingar, frågor och andra resurser som är kopplade till det aktuella kontot.

   **Läs** – när du delar den skrivskyddade URL: en med andra användare kan de se databaser, samlingar, frågor och andra resurser som är kopplade till det aktuella kontot. Om du vill dela resultatet av en fråga med dina gruppmedlemmar som inte har åtkomst till Azure portal eller Azure Cosmos DB-kontot kan du exempelvis ange dem med den här URL: en.

   Välj den typ av åtkomst du vill öppna kontot med och klicka på **Öppna**. När du öppnar du Utforskaren är upplevelsen densamma som du hade med fliken Datautforskaren i Azure-portalen.   

   ![Öppna Azure Cosmos DB explorer](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Kända problem

För närvarande är den **Öppna hel skärms** upplevelsen som gör att du kan dela temporär Läs-och Läs behörighet ännu inte stöd för Azure Cosmos DB Gremlin-och tabell-API-konton. Du kan fortfarande visa dina Gremlin och tabell-API-konton genom att skicka anslutningssträngen till Azure Cosmos DB Explorer. 

För närvarande stöds inte visning av dokument som innehåller ett UUID i Datautforskaren. Detta påverkar inte inläsning av samlingar, endast enskilda dokument eller frågor som innehåller dessa dokument. För att kunna visa och hantera dessa dokument bör användarna fortsätta att använda verktyget som ursprungligen användes för att skapa dessa dokument.

## <a name="next-steps"></a>Nästa steg
Bredvid nu när du har lärt dig hur du kommer igång med Azure Cosmos DB explorer för att hantera dina data, kan du:

* Börja definiera [frågor](sql-api-query-reference.md) med SQL-syntax och utför [Server sidans programmering](stored-procedures-triggers-udfs.md) genom att använda lagrade procedurer, UDF: er, utlösare. 
