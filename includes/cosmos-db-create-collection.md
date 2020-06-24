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
ms.openlocfilehash: 076afcfc8abf4e90d3ccedbb26653656032e59be
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2020
ms.locfileid: "85115403"
---
Du kan nu använda Datautforskaren-verktyget i Azure Portal för att skapa en databas och behållare. 

1. Välj **datautforskaren**  >  **ny behållare**. 
    
    Avsnittet **Lägg till behållare** visas längst till höger. du kan behöva rulla åt höger för att se det.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png" alt-text="Datautforskaren på Azure-portalen, fönstret Lägg till container":::

2. På sidan **Lägg till behållare** anger du inställningarna för den nya behållaren.

    |Inställningen|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|Aktiviteter|Ange *Uppgifter* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrol lera **data flödes alternativet etablera databas** så att du kan dela det data flöde som har etablerats till databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnads besparingar. |
    |**Dataflöde**|400|Lämna data flödet på 400 enheter för programbegäran per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Container-ID**|Poster|Ange *objekt* som namn på den nya behållaren. För container-ID:n gäller samma teckenkrav som för databasnamn.|
    |**Partitionsnyckeln**| /category| Exemplet som beskrivs i den här artikeln använder */Category* som partitionsnyckel.|
    
    Förutom de föregående inställningarna kan du också lägga till **unika nycklar** för behållaren. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. Genom att skapa en unik nyckel princip när du skapar en behållare, säkerställer du att ett eller flera värden är unika per partitionsnyckel. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Välj **OK**. Datautforskaren visar den nya databasen och containern.