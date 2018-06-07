---
title: Hantera en Azure DB som Cosmos-konto via Azure Portal | Microsoft Docs
description: Lär dig hur du hanterar Azure DB som Cosmos-konto via Azure Portal. Hitta en vägledning om hur du använder Azure-portalen visa, kopiera, ta bort och ha åtkomst till konton.
keywords: Azure Portal, azure, Microsoft azure
services: cosmos-db
author: kirillg
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: kirillg
ms.openlocfilehash: 8b28143dc92fa526b631baf6d47e4a9f2367ee0e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34612164"
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Så här hanterar du ett konto i Azure Cosmos DB
Lär dig mer om att ställa in globalt konsekvensfel, fungerar med nycklar och ta bort ett Azure DB som Cosmos-konto i Azure-portalen.

## <a id="consistency"></a>Hantera Azure Cosmos DB konsekvenskontroll inställningar
Att välja rätt konsekvensnivå beror på semantiken för ditt program. Bekanta dig med tillgängliga konsekvensnivåer i Azure Cosmos-databasen genom att läsa [maximerar tillgänglighet och prestanda i Azure Cosmos-databasen med hjälp av konsekvensnivåer][consistency]. Azure Cosmos-DB ger konsekvens, tillgänglighet och prestanda garantier, på alla nivåer för konsekvenskontroll som är tillgängliga för ditt konto. Konfigurera ditt konto med en konsekvenskontroll nivå av starka kräver att data är begränsat till en enda Azure region och globalt inte tillgänglig. Å andra sidan, Avslappnad konsekvensnivåer - avgränsas föråldrad, session eller eventuell aktivera du associera valfritt antal Azure-regioner med ditt konto. Följande enkla steg visar hur du väljer standardnivån för konsekvens för ditt konto.

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Ange standardkonsekvensen för ett konto i Azure Cosmos DB
1. I den [Azure-portalen](https://portal.azure.com/), komma åt ditt konto i Azure Cosmos DB.
2. På kontosidan klickar du på **standard konsekvenskontroll**.
3. I den **standard konsekvenskontroll** väljer du den nya konsekvensnivå och klicka på **spara**.
    ![Standard konsekvenskontroll session][5]

## <a id="keys"></a>Visa, kopiera och återskapa åtkomstnycklar och lösenord
När du skapar ett konto i Azure Cosmos DB tjänsten genererar två åtkomstnycklar för master (eller två lösenorden för MongoDB-API) som kan användas för autentisering vid åtkomst av Azure DB som Cosmos-konto. Genom att tillhandahålla två åtkomstnycklar kan Azure Cosmos DB du återskapa nycklarna utan avbrott i Azure DB som Cosmos-konto. 

I den [Azure-portalen](https://portal.azure.com/), åtkomst på **nycklar** sida på menyn resurs på den **Azure Cosmos DB konto** sidan om du vill visa, kopiera och återskapa åtkomstnycklar som används för att komma åt ditt konto i Azure Cosmos DB. För MongoDB-API-konton, komma åt den **anslutningssträngen** sida från resurs-menyn för att visa, kopiera och återskapa de lösenord som används för att komma åt ditt konto.

![Azure portal skärmbilden nycklar sida](./media/manage-account/keys.png)

> [!NOTE]
> Den **nycklar** sidan innehåller också primära och sekundära anslutningssträngar som kan användas för att ansluta till ditt konto från den [Datamigreringsverktyg](import-data.md).
> 
> 

Skrivskyddad är också tillgängliga på den här sidan. Läsning och frågor är skrivskyddade åtgärder stund skapar, tar bort, inte och ersätter.

### <a name="copy-an-access-key-or-password-in-the-azure-portal"></a>Kopiera en åtkomstnyckel eller lösenord i Azure-portalen
På den **nycklar** sidan (eller **anslutningssträngen** för MongoDB-API-konton), klickar du på den **kopiera** knappen till höger om nyckeln eller lösenordet som du vill kopiera.

![Visa och kopiera åtkomstnyckeln i Azure Portal, sidan Nycklar](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys-and-passwords"></a>Återskapa åtkomstnycklar och lösenord
Du bör ändra snabbtangenter (och lösenorden för MongoDB-API) till Azure DB som Cosmos-konto med jämna mellanrum för att skydda dina anslutningar. Två åtkomst nycklar lösenord tilldelas så att du kan upprätthålla anslutningar till Azure Cosmos DB kontot den ena åtkomstnyckeln medan du återskapar den andra åtkomstnyckeln.

> [!WARNING]
> Återskapar åtkomstnycklarna påverkar alla program som är beroende av den aktuella nyckeln. Alla klienter som använder snabbtangenten för att få åtkomst till Azure DB som Cosmos-kontot måste uppdateras för att använda den nya nyckeln.
> 
> 

Om du har program eller molntjänster med hjälp av Azure DB som Cosmos-konto kan förlorar du anslutningar om du återskapa nycklar, såvida inte du lanserar dina nycklar. Följande steg beskriver processen ingår i rullande dina nycklar/lösenord.

1. Uppdatera åtkomstnyckeln i din programkod för att referera till den sekundära åtkomstnyckeln för kontot Azure Cosmos DB.
2. Återskapa den primära åtkomstnyckeln för ditt Azure DB som Cosmos-konto. I den [Azure-portalen](https://portal.azure.com/), komma åt ditt konto i Azure Cosmos DB.
3. I den **Azure Cosmos-DB konto** klickar du på **nycklar** (eller **anslutningssträngen** för MongoDB konton **).
4. På den **nycklar**/**anslutningssträngen** sidan, klicka på knappen Generera och klicka på **Ok** att bekräfta att du vill skapa en ny nyckel.
    ![Återskapa åtkomstnycklar](./media/manage-account/regenerate-keys.png)
5. När du har verifierat att den nya nyckeln är tillgänglig för användning (cirka fem minuter efter återskapandet), uppdatera åtkomstnyckeln i din programkod för att referera till den nya primärnyckeln.
6. Återskapa den sekundära åtkomstnyckeln.
   
    ![Återskapa åtkomstnycklar](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Det kan ta flera minuter innan en nyligen skapade nyckeln kan användas för att komma åt Azure DB som Cosmos-kontot.
> 
> 

## <a name="get-the-connection-string"></a>Hämta anslutningssträngen
Hämta din anslutningssträng genom att göra följande: 

1. I den [Azure-portalen](https://portal.azure.com), komma åt ditt konto i Azure Cosmos DB.
2. Resurs-menyn klickar du på **nycklar** (eller **anslutningssträngen** för MongoDB-API-konton).
3. Klicka på den **kopiera** knappen bredvid den **primära anslutningssträngen** eller **sekundära anslutningssträngen** rutan. 

Om du använder anslutningssträngen i den [Migreringsverktyget för Azure Cosmos DB databasen](import-data.md), Lägg till namnet på databasen i slutet av anslutningssträngen. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a> Ta bort ett Azure DB som Cosmos-konto
Om du vill ta bort ett Azure DB som Cosmos-konto i Azure Portal som du inte längre använder, högerklicka på namnet på kontot och klicka på **ta bort kontot**.

![Hur du tar bort ett Azure DB som Cosmos-konto i Azure-portalen](./media/manage-account/deleteaccount.png)

1. I den [Azure-portalen](https://portal.azure.com/), få åtkomst till Azure DB som Cosmos-kontot som du vill ta bort.
2. På den **Azure Cosmos DB konto** sidan, högerklicka på kontot och klicka sedan på **ta bort konto**. 
3. Ange namnet på Azure Cosmos DB och bekräfta att du vill ta bort kontot på bekräftelsesidan resulterande.
4. Klicka på den **ta bort** knappen.

![Hur du tar bort ett Azure DB som Cosmos-konto i Azure-portalen](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Nästa steg
Lär dig hur du [komma igång med ditt konto i Azure Cosmos DB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
