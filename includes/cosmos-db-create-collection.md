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
ms.openlocfilehash: 9bc5be37f3892186233fac197c08066dbfacb43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77780730"
---
Du kan nu använda datautforskaren verktyget i Azure-portalen för att skapa en databas och behållare. 

1. Välj**ny behållare för** **datautforskaren** > . 
    
    Området **Lägg till behållare** visas längst till höger, du kan behöva rulla åt höger för att se det.

    ![Datautforskaren på Azure-portalen, fönstret Lägg till container](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. På sidan **Lägg till behållar** anger du inställningarna för den nya behållaren.

    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|Aktiviteter|Ange *Uppgifter* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrollera **alternativet Etablera databasdataflöde,** det låter dig dela dataflödet som etablerats i databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnadsbesparingar. |
    |**Dataflöde**|400|Lämna dataflödet på 400 begäranheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Container-ID**|Objekt|Ange *objekt* som namn på den nya behållaren. För container-ID:n gäller samma teckenkrav som för databasnamn.|
    |**Partitionsnyckeln**| /category| I exemplet som beskrivs i den här artikeln används */category* som partitionsnyckel.|
    
    Förutom föregående inställningar kan du också lägga till **unika nycklar** för behållaren. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. Genom att skapa en unik nyckelprincip när du skapar en behållare säkerställer du att ett eller flera värden per partitionsnyckel är unika. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Välj **OK**. Datautforskaren visar den nya databasen och containern.