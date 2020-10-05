---
title: inkludera fil
description: inkludera fil
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 837231ca07abcfdbd6ce932bb24bd890d91506a8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "69541553"
---
Nu kan du använda datautforskarverktyget i Azure Portal för att skapa en grafdatabas. 

1. Välj **datautforskaren**  >  **nytt diagram**.

    Området **Lägg till diagram** visas längst till höger, du kan behöva bläddra åt höger för att se det.

    ![Datautforskaren i Azure Portal, sidan Lägg till diagram](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. På sidan **Lägg till diagram** anger du inställningarna för den nya grafen.

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Databas-id|sample-database|Ange *sample-database* som namn på den nya databasen. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla `/ \ # ?` eller avslutande blanksteg.
    Dataflöde|400 RU:er|Ändra genomflödet till 400 begäransenheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.
    Graf-id|sample-graph|Ange *sample-graph* som namnet på den nya samlingen. Samma teckenkrav gäller för diagramnamn som databas-ID:n.
    Partition Key (Partitionsnyckel)| /pk |Alla Cosmos DB-konton behöver en partitionsnyckel för att skalas vågrätt. Lär dig hur du väljer en lämplig partitionsnyckel i [artikeln diagram data partitionering](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

3. När formuläret har fyllts i väljer du **OK**.
