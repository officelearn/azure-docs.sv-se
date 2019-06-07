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
ms.openlocfilehash: 45fa6a332697cf298b2446212701025007682357
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754251"
---
Du kan nu använda datautforskarverktyget i Azure-portalen för att skapa en databas och behållare. 

1. Välj **Datautforskaren** > **ny behållare**. 
    
    Den **Lägg till behållare** området visas längst till höger, du kan behöva rulla för att se den.

    ![Azure-portalen Datautforskaren fönstret Lägg till behållare](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. I den **Lägg till behållaren** anger inställningarna för den nya behållaren.

    |Inställning|Föreslaget värde|Beskrivning
    |---|---|---|
    |**Databas-ID**|Uppgifter|Ange *ToDoList* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg. Kontrollera den **etablera databasen dataflöde** alternativ, kan du dela dataflödet som tillhandahållits till databasen över alla behållare i databasen. Det här alternativet hjälper också med kostnadsbesparingar. |
    |**Dataflöde**|400|Vill du lämna dataflödet på 400 begäransenheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden.| 
    |**Behållar-ID**|Objekt|Ange *objekt* som namn på den nya behållaren. Behållare-ID: N har samma teckenkrav gäller som databasnamn.|
    |**Partitionsnyckel**| /category| I exemplet som beskrivs i den här artikeln används */category* som partitionsnyckel.|
    
    Utöver föregående inställningar du kan du lägga till **unika nycklar** för behållaren. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. Genom att skapa en unik nyckel princip när du skapar en behållare kan du se till att ett eller flera värden per partitionsnyckel är unikt. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Välj **OK**. Datautforskaren visar den nya databasen och behållare.