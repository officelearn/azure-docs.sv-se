---
title: Hantera ett Azure Cosmos DB-konto via Azure-portalen | Microsoft Docs
description: Lär dig mer om att hantera ditt Azure Cosmos DB-konto via Azure-portalen. Hitta vägledning om hur du använder Azure Portal för att visa, kopiera, ta bort och få åtkomst till konton.
keywords: Azure-portalen, azure, Microsoft azure
services: cosmos-db
author: kirillg
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: kirillg
ms.openlocfilehash: abcf51c6bd196c2ffb0bb35e2df161531a53972d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229398"
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Så här hanterar du ett Azure Cosmos DB-konto
Lär dig mer om att ställa in globala konsekvens, arbeta med nycklar och ta bort ett Azure Cosmos DB-konto i Azure-portalen.

## <a id="consistency"></a>Hantera inställningar för Azure Cosmos DB-konsekvens
Att välja rätt konsekvensnivå beror på semantiken för ditt program. Bekanta dig med de tillgängliga konsekvensnivåerna i Azure Cosmos DB genom att läsa [maximerar tillgänglighet och prestanda i Azure Cosmos DB med hjälp av konsekvensnivåer][consistency]. Azure Cosmos DB tillhandahåller konsekvens, tillgänglighet och prestanda garantier på varje konsekvensnivå som är tillgängliga för ditt databaskonto. Konfigurera ditt databaskonto med en konsekvensnivå av stark kräver att dina data är begränsat till en enda Azure-region och globalt finns inte. Å andra sidan, Avslappnad konsekvensnivåer – begränsad föråldring, session och slutlig aktivera du associera valfritt antal Azure-regioner med ditt databaskonto. Följande enkla steg visar hur du väljer standardkonsekvensnivå för ditt databaskonto.

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Ange standardkonsekvensen för ett Azure Cosmos DB-konto
1. I den [Azure-portalen](https://portal.azure.com/), åtkomst till ditt Azure Cosmos DB-konto.
2. På sidan klickar du på **Standardkonsekvens**.
3. I den **Standardkonsekvens** väljer du den nya konsekvensnivån och klicka på **spara**.
    ![Standard konsekvens session][5]

## <a id="keys"></a>Visa, kopiera och återskapa åtkomstnycklar och lösenord
När du skapar ett Azure Cosmos DB-konto kan tjänsten genererar två åtkomstnycklar för master (eller två lösenord för MongoDB API-konton) som kan användas för autentisering när Azure Cosmos DB-konto används. Genom att tillhandahålla två åtkomstnycklar för kan Azure Cosmos DB du återskapa nycklarna utan avbrott på ditt Azure Cosmos DB-konto. 

I den [Azure-portalen](https://portal.azure.com/), åtkomst till den **nycklar** sidan från resurs-menyn på den **Azure Cosmos DB-konto** sidan för att visa, kopiera och återskapa åtkomstnycklarna som är vana vid åtkomst till ditt Azure Cosmos DB-konto. MongoDB API-konton kan du få åtkomst till den **Connection String** sida från resursmenyn för att visa, kopiera och återskapa de lösenord som används för att komma åt ditt konto.

![Azure portal skärmbild, sidan nycklar](./media/manage-account/keys.png)

> [!NOTE]
> Den **nycklar** sidan finns också primära och sekundära anslutningssträngar som kan användas för att ansluta till ditt konto från den [Datamigreringsverktyget](import-data.md).
> 
> 

Skrivskyddade nycklar är också tillgängliga på den här sidan. Läsningar och frågor skrivskyddade åtgärder tag skapar, tar bort, och ersätter finns inte.

### <a name="copy-an-access-key-or-password-in-the-azure-portal"></a>Kopiera en åtkomstnyckel eller lösenord i Azure portal
På den **nycklar** sida (eller **anslutningssträngen** för MongoDB-API-konton), klickar du på den **kopia** knappen till höger om nyckeln eller lösenordet som du vill kopiera.

![Visa och kopiera åtkomstnyckeln i Azure Portal, sidan Nycklar](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys-and-passwords"></a>Återskapa åtkomstnycklar och lösenord
Du bör ändra åtkomstnycklar (och lösenord för MongoDB API-konton) till ditt Azure Cosmos DB-konto med jämna mellanrum för att skydda dina anslutningar. Två nycklar/lösenord för åtkomst tilldelas så att du kan upprätthålla anslutningar till Azure Cosmos DB-kontot som den ena åtkomstnyckeln medan du återskapar den andra åtkomstnyckeln.

> [!WARNING]
> Återskapar åtkomstnycklarna påverkar alla program som är beroende av den aktuella nyckeln. Alla klienter som använder åtkomstnycklar för att få åtkomst till Azure Cosmos DB-kontot måste uppdateras för att använda den nya nyckeln.
> 
> 

Om du har program eller cloud services med Azure Cosmos DB-kontot, förlorar du anslutningarna om du återskapar nycklar, såvida inte du distribuerar dina nycklar. Följande steg beskriver processen som ingår i löpande dina nycklar/lösenord.

1. Uppdatera åtkomstnyckel i din programkod för att referera till den sekundära åtkomstnyckeln för Azure Cosmos DB-konto.
2. Återskapa den primära åtkomstnyckeln för ditt Azure Cosmos DB-konto. I den [Azure-portalen](https://portal.azure.com/), åtkomst till ditt Azure Cosmos DB-konto.
3. I den **Azure Cosmos DB-konto** klickar du på **nycklar** (eller **Connection String** för MongoDB-konton **).
4. På den **nycklar**/**Connection String** sidan, klicka på knappen Generera och sedan på **Ok** att bekräfta att du vill skapa en ny nyckel.
    ![Återskapa åtkomstnycklar](./media/manage-account/regenerate-keys.png)
5. När du har verifierat att den nya nyckeln är tillgänglig för användning (cirka fem minuter efter återskapas), uppdatera åtkomstnyckel i din programkod för att referera till den nya primärnyckeln.
6. Återskapa den sekundära åtkomstnyckeln.
   
    ![Återskapa åtkomstnycklar](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Det kan ta flera minuter innan en nyligen genererad nyckel kan användas för åtkomst till ditt Azure Cosmos DB-konto.
> 
> 

## <a name="get-the-connection-string"></a>Hämta anslutningssträngen
Hämta din anslutningssträng genom att göra följande: 

1. I den [Azure-portalen](https://portal.azure.com), åtkomst till ditt Azure Cosmos DB-konto.
2. I resurs-menyn klickar du på **nycklar** (eller **Connection String** för MongoDB API-konton).
3. Klicka på den **kopia** knappen bredvid den **primär anslutningssträng** eller **sekundär anslutningssträng** box. 

Om du använder anslutningssträngen i den [Migreringsverktyget för Azure Cosmos DB-databas](import-data.md), lägga till namnet på databasen i slutet av anslutningssträngen. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a> Ta bort ett Azure Cosmos DB-konto
Om du vill ta bort ett Azure Cosmos DB-konto från Azure-portalen som du inte längre använder, högerklicka på namnet på kontot och klicka på **ta bort konto**.

![Hur du tar bort ett Azure Cosmos DB-konto i Azure portal](./media/manage-account/deleteaccount.png)

1. I den [Azure-portalen](https://portal.azure.com/), åtkomst till Azure Cosmos DB-kontot som du vill ta bort.
2. På den **Azure Cosmos DB-konto** sidan, högerklickar du på kontot och klicka sedan på **ta bort konto**. 
3. På bekräftelsesidan resulterande skriver du namnet på Azure Cosmos DB-kontot att bekräfta att du vill ta bort kontot.
4. Klicka på den **ta bort** knappen.

![Hur du tar bort ett Azure Cosmos DB-konto i Azure portal](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Nästa steg
Lär dig hur du [Kom igång med Azure Cosmos DB-kontot](https://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
