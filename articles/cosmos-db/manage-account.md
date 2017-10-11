---
title: Hantera en Azure DB som Cosmos-konto via Azure Portal | Microsoft Docs
description: "Lär dig hur du hanterar Azure DB som Cosmos-konto via Azure Portal. Hitta en vägledning om hur du använder Azure-portalen visa, kopiera, ta bort och ha åtkomst till konton."
keywords: Azure-portalen documentdb, azure, Microsoft azure
services: cosmos-db
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: kirillg
ms.openlocfilehash: a0c6ec8d490e1adacc96758971ab91d8eaeab45c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Så här hanterar du ett konto i Azure Cosmos DB
Lär dig mer om att ställa in globalt konsekvensfel, fungerar med nycklar och ta bort ett Azure DB som Cosmos-konto i Azure-portalen.

## <a id="consistency"></a>Hantera Azure Cosmos DB konsekvenskontroll inställningar
Att välja rätt konsekvensnivå beror på semantiken för ditt program. Du bör du bekanta dig med tillgängliga konsekvensnivåer i Azure Cosmos-databasen genom att läsa [maximerar tillgänglighet och prestanda i Azure Cosmos-databasen med hjälp av konsekvensnivåer][consistency]. Azure Cosmos-DB ger konsekvens, tillgänglighet och prestanda garantier, på alla nivåer för konsekvenskontroll som är tillgängliga för ditt konto. Konfigurera ditt konto med en konsekvenskontroll nivå av starka kräver att data är begränsat till en enda Azure region och globalt inte tillgänglig. Å andra sidan, Avslappnad konsekvensnivåer - avgränsas föråldrad, session eller eventuell aktivera du associera valfritt antal Azure-regioner med ditt konto. Följande enkla steg visar hur du väljer standardnivån för konsekvens för ditt konto. 

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Ange standardkonsekvensen för ett konto i Azure Cosmos DB
1. I den [Azure-portalen](https://portal.azure.com/), komma åt ditt konto i Azure Cosmos DB.
2. I bladet konto klickar du på **standard konsekvenskontroll**.
3. I den **standard konsekvenskontroll** bladet välj ny konsekvensnivå och klicka på **spara**.
    ![Standard konsekvenskontroll session][5]

## <a id="keys"></a>Visa, kopiera och återskapa åtkomstnycklar
När du skapar ett konto i Azure Cosmos DB genererar tjänsten två master åtkomstnycklar som kan användas för autentisering vid åtkomst av Azure DB som Cosmos-konto. Genom att tillhandahålla två åtkomstnycklar kan Azure Cosmos DB du återskapa nycklarna utan avbrott i Azure DB som Cosmos-konto. 

I den [Azure-portalen](https://portal.azure.com/), åtkomst av **nycklar** bladet på menyn resurs på den **Azure Cosmos DB konto** bladet för att visa, kopiera och återskapa åtkomstnycklar som används för att komma åt ditt konto i Azure Cosmos DB.

![Skärmbild av Azure Portal, bladet nycklar](./media/manage-account/keys.png)

> [!NOTE]
> Den **nycklar** bladet innehåller också primära och sekundära anslutningssträngar som kan användas för att ansluta till ditt konto från den [Datamigreringsverktyg](import-data.md).
> 
> 

Skrivskyddade nycklar är också tillgängliga på det här bladet. Läsning och frågor är skrivskyddade åtgärder stund skapar, tar bort, inte och ersätter.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Kopiera en snabbtangent i Azure Portal
På den **nycklar** bladet, klickar du på den **kopiera** knappen till höger om den nyckel som du vill kopiera.

![Visa och kopiera snabbtangent i bladet nycklar i Azure-portalen](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar
Du bör ändra åtkomstnycklarna till ditt Azure DB som Cosmos-konto med jämna mellanrum för att skydda dina anslutningar. Två åtkomstnycklar tilldelas så att du kan upprätthålla anslutningar till Azure Cosmos DB kontot den ena åtkomstnyckeln medan du återskapar den andra åtkomstnyckeln.

> [!WARNING]
> Återskapar åtkomstnycklarna påverkar alla program som är beroende av den aktuella nyckeln. Alla klienter som använder snabbtangenten för att få åtkomst till Azure DB som Cosmos-kontot måste uppdateras för att använda den nya nyckeln.
> 
> 

Om du har program eller molntjänster med hjälp av Azure DB som Cosmos-konto kan förlorar du anslutningar om du återskapa nycklar, såvida inte du lanserar dina nycklar. Följande steg beskriver processen ingår i rullande dina nycklar.

1. Uppdatera åtkomstnyckeln i din programkod för att referera till den sekundära åtkomstnyckeln för kontot Azure Cosmos DB.
2. Återskapa den primära åtkomstnyckeln för ditt Azure DB som Cosmos-konto. I den [Azure Portal](https://portal.azure.com/), komma åt ditt konto i Azure Cosmos DB.
3. I den **Azure Cosmos-DB konto** bladet, klickar du på **nycklar**.
4. På den **nycklar** bladet, klickar du på knappen Generera, och klicka sedan på **Ok** att bekräfta att du vill skapa en ny nyckel.
    ![Återskapa åtkomstnycklar](./media/manage-account/regenerate-keys.png)
5. När du har verifierat att den nya nyckeln är tillgänglig för användning (cirka 5 minuter efter återskapandet), uppdatera åtkomstnyckeln i din programkod för att referera till den nya primärnyckeln.
6. Återskapa den sekundära åtkomstnyckeln.
   
    ![Återskapa åtkomstnycklar](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Det kan ta flera minuter innan en nyligen skapade nyckeln kan användas för att komma åt Azure DB som Cosmos-kontot.
> 
> 

## <a name="get-the--connection-string"></a>Hämta anslutningssträngen
Hämta din anslutningssträng genom att göra följande: 

1. I den [Azure-portalen](https://portal.azure.com), komma åt ditt konto i Azure Cosmos DB.
2. Resurs-menyn klickar du på **nycklar**.
3. Klicka på den **kopiera** knappen bredvid den **primära anslutningssträngen** eller **sekundära anslutningssträngen** rutan. 

Om du använder anslutningssträngen i den [Migreringsverktyget för Azure Cosmos DB databasen](import-data.md), Lägg till namnet på databasen i slutet av anslutningssträngen. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a>Ta bort ett Azure DB som Cosmos-konto
Om du vill ta bort ett Azure DB som Cosmos-konto från Azure-portalen som du inte längre använder högerklickar du på namnet på kontot och klicka på **ta bort kontot**.

![Hur du tar bort ett Azure DB som Cosmos-konto i Azure Portal](./media/manage-account/deleteaccount.png)

1. I den [Azure-portalen](https://portal.azure.com/), få åtkomst till Azure DB som Cosmos-kontot som du vill ta bort.
2. På den **Azure Cosmos DB konto** bladet högerklickar du på kontot och klicka sedan på **ta bort konto**. 
3. Skriv namnet på Azure Cosmos DB och bekräfta att du vill ta bort kontot på bladet resulterande bekräftelse.
4. Klicka på den **ta bort** knappen.

![Hur du tar bort ett Azure DB som Cosmos-konto i Azure Portal](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Nästa steg
Lär dig hur du [komma igång med ditt konto i Azure Cosmos DB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
