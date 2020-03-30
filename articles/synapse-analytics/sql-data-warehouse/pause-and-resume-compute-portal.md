---
title: Pausa och återuppta beräkning i Synapse SQL-pool via Azure-portalen
description: Använd Azure-portalen för att pausa beräkning för SQL-pool för att spara kostnader. Återuppta beräkningen när du är redo att använda informationslagret.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 55e3d5bf4fb63c35d484e4a764c7eeb2e2484fcf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350965"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Snabbstart: Pausa och återuppta beräkning i Synapse SQL-pool via Azure-portalen

Du kan använda Azure-portalen för att pausa och återuppta beräkningsresurserna för Synapse SQL-pool (datalager). Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Använd [Skapa och anslut - portal för](create-data-warehouse-portal.md) att skapa en SQL-pool som heter **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausa beräkning

Om du vill minska kostnaderna kan du pausa och återuppta beräkningsresurser på begäran. Om du till exempel inte använder databasen under natten och på helgerna kan du pausa den under dessa tider och återuppta den under dagen. 
>[!NOTE]
>Du debiteras inte för beräkningsresurser medan databasen pausas. Du kommer dock att fortsätta att debiteras för lagring. 

Så här pausar du en SQL-pool:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på den vänstra navigeringssidan i Azure-portalen.
2. Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW)** för att öppna SQL-poolen. 
3. På **mySampleDataWarehouse** sida, meddelande **Status** är **online**.

    ![Beräkna online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Om du vill pausa SQL-poolen klickar du på knappen **Pausa.** 
5. En bekräftelsefråga visas som frågar om du vill fortsätta. Klicka på **Ja**.
6. Vänta en stund och lägg sedan märke till att **statusen** **pausas**.

    ![Pausar](./media/pause-and-resume-compute-portal/pausing.png)

7. När pausningen är klar är statusen **Pausad** och alternativknappen **är Återuppta**.
8. Beräkningsresurserna för SQL-poolen är nu offline. Du debiteras inte för beräkning förrän du återupptar tjänsten.

    ![Beräkna offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Återuppta beräkning

Följ dessa steg för att återuppta SQL-poolen.

1. Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på vänster sida i Azure-portalen.
2. Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW)** för att öppna SQL-poolsidan. 
3. På **sidan mySampleDataWarehouse** är meddelande **Status** **pausad**.

    ![Beräkna offline](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Om du vill återuppta SQL-poolen klickar du på **Återuppta**. 
5. En bekräftelsefråga visas som frågar om du vill starta. Klicka på **Ja**.
6. Observera **att statusen** **återupptas**.

    ![Återupptar](./media/pause-and-resume-compute-portal/resuming.png)

7. När SQL-poolen är online igen är statusen **Online** och alternativknappen är **Paus**.
8. Beräkningsresurserna för SQL-poolen är nu online och du kan använda tjänsten. Avgifterna för beräkning har återupptagits.

    ![Beräkna online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och data som lagras i din SQL-pool. Dessa beräknings- och lagringsresurser debiteras separat. 

- Om du vill behålla data i lagring, pausa beräkning.
- Om du vill ta bort framtida avgifter kan du ta bort SQL-poolen. 

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure-portalen](https://portal.azure.com)och klicka på din SQL-pool.

    ![Rensa resurser](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. 

2. Om du vill ta bort SQL-poolen så att du inte debiteras för beräkning eller lagring klickar du på **Ta bort**.

3. Om du vill ta bort den SQL-server som du skapade klickar du **på sqlpoolservername.database.windows.net**och sedan på **Ta bort**.  

   > [!CAUTION]
   > Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.


## <a name="next-steps"></a>Nästa steg

Du har nu pausat och återupptagit beräkningen för din SQL-pool. Fortsätt till nästa artikel om du vill veta mer om hur [du läser in data i SQL-poolen](load-data-from-azure-blob-storage-using-polybase.md). Mer information om hur du hanterar beräkningsfunktioner finns i artikeln [Hantera beräkningsöversikt.](sql-data-warehouse-manage-compute-overview.md) 

