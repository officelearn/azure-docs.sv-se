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
ms.openlocfilehash: c3cbfda674abaeea1adf35c3ee0d2b5ddf6b2f84
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853487"
---
Nu kan du använda datautforskarverktyget i Azure Portal för att skapa en databas och samling. 

1. Klicka på **Datautforskaren** > **Ny samling**. 
    
    Området **Lägg till samling** visas längst till höger, du kan behöva bläddra åt höger för att se det.

    ![Datautforskaren i Azure-portalen, bladet Lägg till samling](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. På sidan **Lägg till samling** anger du inställningarna för den nya samlingen.

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Databas-id|*Uppgifter*|Ange *Uppgifter* som namn på den nya databasen. Databasnamn måste innehålla mellan 1 och 255 tecken och får inte innehålla /, \\, #, ? eller avslutande blanksteg.
    Samlings-id|*Objekt*|Ange *Objekt* som namnet på din nya samling. Samma teckenkrav gäller för samlings-ID:n som databasnamn.
    Etablera databasens dataflöde|Lämna tomt|Azure Cosmos DB kan etablera dataflöde på databasnivå (alla samlingar i en databas delar samma dataflöde) eller på samlingsnivå. Lämna tomt för att etablera dataflöde på samlingsnivå för den här samlingen.
    Lagringskapacitet|*Obegränsat*|Välj lagringskapaciteten **Obegränsat**. 
    Partitionsnyckeln|*/category*|Ange ”/category” som partitionsnyckel. Om du anger en partitionsnyckel kan Azure Cosmos DB skala samlingen så att den uppfyller programmets lagrings- och dataflödesbehov. I allmänhet är ett bra val av partitionsnyckel en som har en mängd olika distinkta värden, och ger en jämn fördelning av lagrings- och begärandevolym över arbetsbelastningen. [Läs mer om partitionering.](../articles/cosmos-db/partitioning-overview.md)
    Dataflöde|*400 RU/s*|Ändra genomflödet till 400 begäransenheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden. 
    
    Utöver föregående inställningar kan du lägga till **unika nycklar** för samlingen om du vill. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. När du skapar en unik nyckelprincip medan du skapar en samling garanterar du unikheten för ett eller flera värden per partitionsnyckel. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Klicka på **OK**.

    Datautforskaren visar den nya databasen och samlingen.

    ![Datautforskaren i Azure-portalen visar den nya databasen och samlingen](./media/cosmos-db-create-collection/azure-cosmos-db-data-explorer-preview.png)