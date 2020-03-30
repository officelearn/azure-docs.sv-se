---
title: Använda Azure Cosmos DB Explorer för att hantera dina data
description: Azure Cosmos DB explorer är ett fristående webbaserat gränssnitt som gör att du kan visa och hantera data som lagras i Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096812"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Arbeta med data med hjälp av Azure Cosmos Explorer 

Azure Cosmos DB explorer är ett fristående webbaserat gränssnitt som gör att du kan visa och hantera data som lagras i Azure Cosmos DB. Azure Cosmos DB explorer motsvarar den befintliga **fliken Data Explorer** som är tillgänglig i Azure-portalen när du skapar ett Azure Cosmos DB-konto. De viktigaste fördelarna med Azure Cosmos DB explorer jämfört med den befintliga datautforskaren är:

* Du har en helskärmsfastighet för att visa dina data, köra frågor, lagrade procedurer, utlösare och visa deras resultat.  

* Du kan ge tillfällig eller permanent läs- eller lässkrivningsåtkomst till ditt databaskonto och dess samlingar till andra användare som inte har åtkomst till Azure-portalen eller prenumerationen.  

* Du kan dela frågeresultaten med andra användare som inte har åtkomst till Azure-portal eller prenumeration.  

## <a name="access-azure-cosmos-db-explorer"></a>Få tillgång till Azure Cosmos DB-utforskare

1. Logga in på [Azure Portal](https://portal.azure.com/). 

2. Från **Alla resurser**kan du söka efter och navigera till ditt Azure Cosmos DB-konto, välja Nycklar och kopiera den primära **anslutningssträngen**.  

3. Gå https://cosmos.azure.com/till , klistra in anslutningssträngen och välj **Anslut**. Med hjälp av anslutningssträngen kan du komma åt Azure Cosmos DB explorer utan några tidsgränser.  

   Om du vill ge andra användare tillfällig åtkomst till ditt Azure Cosmos DB-konto kan du göra det med hjälp av read-write och read access-URL:er. 

4. Öppna **bladet Data Explorer,** välj **Öppna helskärm**. I popup-dialogrutan kan du visa två åtkomstadresser – **Läs-skriv** och **läsa**. Med dessa webbadresser kan du tillfälligt dela ditt Azure Cosmos DB-konto med andra användare. Åtkomsten till kontot upphör att gälla inom 24 timmar varefter du kan återansluta med hjälp av en ny åtkomst-URL eller anslutningssträngen. 

   **Läs-skriv** – När du delar read-write-URL:en med andra användare kan de visa och ändra databaser, samlingar, frågor och andra resurser som är associerade med det specifika kontot.

   **Läs** - När du delar den skrivskyddade webbadressen med andra användare kan de visa databaser, samlingar, frågor och andra resurser som är kopplade till det specifika kontot. Om du till exempel vill dela resultaten av en fråga med dina lagkamrater som inte har åtkomst till Azure-portalen eller ditt Azure Cosmos DB-konto kan du ange den här URL:en.

   Välj den typ av åtkomst som du vill öppna kontot med och klicka på **Öppna**. När du har öppnat utforskaren är upplevelsen densamma som du hade med fliken Data Explorer i Azure-portalen.   

   ![Öppna Azure Cosmos DB explorer](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Kända problem

För närvarande stöds inte den **öppna helskärmsfunktionen** som gör att du kan dela tillfällig läs- eller läsåtkomst för Azure Cosmos DB Gremlin- och Table API-konton. Du kan fortfarande visa dina Gremlin- och Table API-konton genom att skicka anslutningssträngen till Azure Cosmos DB Explorer. 

För närvarande stöds inte visning av dokument som innehåller ett UUID i Data Explorer. Detta påverkar inte inläsning av samlingar, bara visa enskilda dokument eller frågor som innehåller dessa dokument. Om du vill visa och hantera dessa dokument bör användarna fortsätta att använda verktyget som ursprungligen användes för att skapa dessa dokument.

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du kommer igång med Azure Cosmos DB explorer för att hantera dina data, kan du nästa du:

* Börja definiera [frågor](sql-api-query-reference.md) med SQL-syntax och utför [programmering på serversidan](stored-procedures-triggers-udfs.md) med hjälp av lagrade procedurer, UDF-utlösare. 
