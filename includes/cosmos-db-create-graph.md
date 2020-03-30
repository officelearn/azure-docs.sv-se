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
ms.openlocfilehash: 837231ca07abcfdbd6ce932bb24bd890d91506a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69541553"
---
Nu kan du använda datautforskarverktyget i Azure Portal för att skapa en grafdatabas. 

1. Välj**nytt diagram** **för datautforskaren** > .

    Området **Lägg till diagram** visas längst till höger, du kan behöva bläddra åt höger för att se det.

    ![Datautforskaren i Azure Portal, sidan Lägg till diagram](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. På sidan **Lägg till diagram** anger du inställningarna för den nya grafen.

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Databas-id|sample-database|Ange *sample-database* som namn på den nya databasen. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla `/ \ # ?` eller avslutande blanksteg.
    Dataflöde|400 RU:er|Ändra genomflödet till 400 begäransenheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.
    Graf-id|sample-graph|Ange *sample-graph* som namnet på den nya samlingen. Samma teckenkrav gäller för grafnamn som databas-id.
    Partitionsnyckel| /pk (pk) |Alla Cosmos DB-konton behöver en partitionsnyckel för att skala horisontellt. Lär dig hur du väljer en lämplig partitionsnyckel i [artikeln Graph Data Partitioning](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

3. När formuläret är ifyllt väljer du **OK**.
