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
ms.openlocfilehash: e6e70da1f939547cdb589ae7bcb6ed1f6148f22e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38733785"
---
Du kan nu använda frågor i Datautforskaren för att hämta och filtrera dina data.

1. Du kan se att frågan är inställd på `SELECT * FROM c` som standard. Den här standardfrågan hämtar och visar alla dokument i samlingen. 

    ![Standardfrågan i Datautforskaren är ”SELECT * FROM c”](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)

2. Stanna kvar på fliken **Dokument** och ändra frågan genom att klicka på knappen **Redigera filter**, lägga till `ORDER BY c._ts DESC` i frågepredikatrutan och sedan klicka på **Tillämpa filter**.

    ![Ändra standardfrågan genom att lägga till ORDER BY c._ts DESC och klicka på Tillämpa filter](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

Den här ändrade frågan visar en lista över dokumenten i fallande ordning baserat på deras tidsstämpel, så att ditt andra dokument nu står först. Om du känner till SQL-syntax kan du ange någon av de [SQL-frågor](../articles/cosmos-db/sql-api-sql-query.md) som stöds i den här rutan. 

Då är vårt arbete i Datautforskaren klart. Innan vi går vidare till att arbeta med kod kan du notera att du även kan använda Datautforskaren för att skapa lagrade procedurer, UDF:er och utlösare för att utföra affärslogik på serversidan såväl som att skala genomflödet. Datautforskaren visar all den inbyggda programmässiga dataåtkomsten som finns tillgänglig i API:erna, men ger enkel åtkomst till dina data i Azure-portalen.