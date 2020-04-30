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
ms.openlocfilehash: 5743d785afb87aef6b3a89af6dc8eb18f66b164d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "68854659"
---
Nu kan du använda Datautforskaren i Azure Portal för att skapa en databas och en tabell. 

1. Välj **datautforskaren** > **ny tabell**. 
    
    Området **Lägg till tabell** visas längst till höger, du kan behöva bläddra åt höger för att se det.

    ![Datautforskaren i Azure Portal](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. På sidan **Lägg till tabell** anger du inställningarna för den nya tabellen.

    Inställningen|Föreslaget värde|Beskrivning
    ---|---|---
    Tabell-id|sample-table|ID för din nya tabell. Samma teckenkrav gäller för tabellnamn som databas-id. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla `/ \ # ?` eller avslutande blanksteg.
    Dataflöde|400 RU:er|Ändra genomflödet till 400 begäransenheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.

3. Välj **OK**.

4. Datautforskaren visar den nya databasen och tabellen.

   ![Datautforskaren i Azure-portalen visar den nya databasen och samlingen](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)