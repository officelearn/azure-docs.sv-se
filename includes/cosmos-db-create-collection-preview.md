---
title: ta med fil
description: ta med fil
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 331886f01345aba576cd8f96f95077f9bbdae704
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68002679"
---
Du kan nu använda Datautforskaren-verktyget i Azure Portal för att skapa en databas och behållare. 

1. Klicka på **datautforskaren** > **ny behållare**. 
    
    Avsnittet **Lägg till behållare** visas längst till höger. du kan behöva rulla åt höger för att se det.

    ![Bladet Azure Portal Datautforskaren, Lägg till behållare](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. På sidan **Lägg till behållare** anger du inställningarna för den nya behållaren.

    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|Uppgifter|Ange *Uppgifter* som namn på den nya databasen. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla /, \\, #, ? eller avslutande blanksteg. Kontrol lera **data flödes alternativet etablera databas** så att du kan dela det data flöde som har etablerats till databasen över alla behållare i databasen. Det här alternativet hjälper också till med kostnads besparingar. |
    |**Dataflöde**|400|Lämna data flödet på 400 enheter för programbegäran per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Container-ID**|Objekt|Ange *objekt* som namn på den nya behållaren. Container-ID: n har samma teckenuppsättnings krav som databas namn.|
    |**Partitionsnyckel**| /category| Exemplet som beskrivs i den här artikeln använder */Category* som partitionsnyckel. Om du anger en partitionsnyckel kan Azure Cosmos DB skala samlingen så att den uppfyller programmets lagrings- och dataflödesbehov. I allmänhet är ett bra val av partitionsnyckel en som har en mängd olika distinkta värden, och ger en jämn fördelning av lagrings- och begärandevolym över arbetsbelastningen. [Läs mer om partitionering.](../articles/cosmos-db/partitioning-overview.md)|
    
    Förutom de föregående inställningarna kan du också lägga till **unika nycklar** för behållaren. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. Genom att skapa en unik nyckel princip när du skapar en behållare, säkerställer du att ett eller flera värden är unika per partitionsnyckel. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Välj **OK**. Datautforskaren visar den nya databasen och behållaren.

