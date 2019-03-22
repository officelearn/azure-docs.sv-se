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
ms.openlocfilehash: a91c42ca32fb356b418dcd412c0690b01ff85789
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908179"
---
Nu kan du använda Datautforskaren i Azure Portal för att skapa en databas och en tabell. 

1. Klicka på **Datautforskaren** > **Ny tabell**. 
    
    Området **Lägg till tabell** visas längst till höger, du kan behöva bläddra åt höger för att se det.

    ![Datautforskaren i Azure-portalen](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. På sidan **Lägg till tabell** anger du inställningarna för den nya tabellen.

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Tabell-id|sample-table|ID för din nya tabell. Samma teckenkrav gäller för tabellnamn som databas-id. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla `/ \ # ?` eller avslutande blanksteg.
    Dataflöde|400 RU:er|Ändra genomflödet till 400 begäransenheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.

3. Klicka på **OK**.

4. Datautforskaren visar den nya databasen och tabellen.

   ![Datautforskaren i Azure-portalen visar den nya databasen och samlingen](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)