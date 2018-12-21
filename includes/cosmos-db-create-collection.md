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
ms.openlocfilehash: e287741fd6643c2eba192a9e29f46219faf520ec
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111766"
---
Nu kan du använda datautforskarverktyget i Azure Portal för att skapa en databas och samling. 

1. Klicka på **Datautforskaren** > **Ny samling**. 
    
    Området **Lägg till samling** visas längst till höger, du kan behöva bläddra åt höger för att se det.

    ![Datautforskaren i Azure-portalen, fönstret Lägg till samling](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. På sidan **Lägg till samling** anger du inställningarna för den nya samlingen.

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Databas-id|Uppgifter|Ange *Uppgifter* som namn på den nya databasen. Databasnamn måste innehålla 1–255 tecken och får inte innehålla `/, \\, #, ?`, eller avslutande blanksteg.
    Samlings-id|Objekt|Ange *Objekt* som namnet på din nya samling. Samma teckenkrav gäller för samlings-ID:n som databasnamn.
    Partitionsnyckeln| <Your partition key>| Ange en partitionsnyckel, exempelvis */userid*.
    Dataflöde|400 RU|Ändra genomflödet till 400 begäransenheter per sekund (RU/s). Du kan skala upp dataflödet senare om du vill minska svarstiden. 
    
    Utöver föregående inställningar kan du lägga till **unika nycklar** för samlingen om du vill. Vi lämnar fältet tomt i det här exemplet. Unika nycklar ger utvecklarna möjlighet att lägga till ett lager med dataintegritet till databasen. När du skapar en unik nyckelprincip medan du skapar en samling garanterar du unikheten för ett eller flera värden per partitionsnyckel. Läs mer i artikeln om [unika nycklar i Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Klicka på **OK**.

    Datautforskaren visar den nya databasen och samlingen.

    ![Datautforskaren i Azure-portalen visar den nya databasen och samlingen](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection.png)