---
title: 'Snabbstart: Pausa och återuppta compute i Azure SQL Data Warehouse - Azure-portalen | Microsoft Docs'
description: Använda Azure portal för att pausa beräkning i Azure SQL Data Warehouse och spara kostnader. Återuppta databearbetning när du är redo att använda datalagret.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 162bc44bccc04d97ea4d631d0e95defa342e6616
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Snabbstart: Pausa och återuppta beräkningen för en Azure SQL Data Warehouse i Azure-portalen
Använda Azure portal för att pausa beräkning i Azure SQL Data Warehouse och spara kostnader. [Återuppta databearbetning](sql-data-warehouse-manage-compute-overview.md) när du är redo att använda datalagret.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Använd [skapa och Connect - portal](create-data-warehouse-portal.md) att skapa ett data warehouse kallas **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausa beräkning
Du kan pausa och återuppta beräkning resurser på begäran för att spara kostnader. Till exempel om du inte använder databasen under natten och helger, kan du pausa under dessa tider och återuppta under dagen. Du kommer inte att debiteras för beräkningsresurser när databasen har pausats. Du kommer dock fortsätta att debiteras för lagring. 

Följ dessa steg om du vill pausa ett SQL data warehouse.

1. Klicka på **SQL-databaser** på den vänstra sidan i Azure-portalen.
2. Välj **mySampleDataWarehouse** på sidan **SQL-databaser**. Informationslagret öppnas. 
3. På den **mySampleDataWarehouse** sidan, meddelande **Status** är **Online**.

    ![Beräkna online](media/pause-and-resume-compute-portal/compute-online.png)

4. Pausa datalagret, klicka på den **pausa** knappen. 
5. En bekräftelse fråga visas som frågar om du vill fortsätta. Klicka på **Ja**.
6. Vänta en stund och kontrollera sedan den **Status** är **pausa**.

    ![Pausar](media/pause-and-resume-compute-portal/pausing.png)

7. När åtgärden pausa är klar är status **pausad** och alternativknappen är **starta**.
8. Beräkningsresurserna för datalagret är nu offline. Du kommer inte att debiteras för beräkningen tills du återuppta tjänsten.

    ![Beräkna offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Återuppta beräkning
Följ dessa steg om du vill återuppta ett SQL data warehouse.

1. Klicka på **SQL-databaser** på den vänstra sidan i Azure-portalen.
2. Välj **mySampleDataWarehouse** på sidan **SQL-databaser**. Informationslagret öppnas. 
3. På den **mySampleDataWarehouse** sidan, meddelande **Status** är **pausad**.

    ![Beräkna offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Om du vill återuppta datalagret klickar du på **starta**. 
5. En bekräftelse fråga visas som frågar om du vill starta. Klicka på **Ja**.
6. Observera den **Status** är **inställd på återupptar**.

    ![Fortsätter](media/pause-and-resume-compute-portal/resuming.png)

7. När datalagret är tillbaka online är status **Online** och alternativknappen är **paus**.
8. Beräkningsresurserna för datalagret är nu online och du kan använda tjänsten. Kostnader för beräkning har återupptagits.

    ![Beräkna online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Rensa resurser

Att debiteras du för informationslagerenheter och data som lagras i datalagret. Dessa beräknings- och lagringsresurser debiteras separat. 

- Om du vill skydda data i lagring pausa beräkning.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret. 

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och klicka på ditt informationslager.

    ![Rensa resurser](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. När informationslagret har pausats visas knappen **Starta**.  Klicka på **Starta** om du vill återuppta beräkningarna.

2. Om du vill ta bort informationslagret så att du varken debiteras för beräkning eller lagring klickar du på **Ta bort**.

3. Ta bort SQL-server som du har skapat, klicka på **mynewserver 20171113.database.windows.net**, och klicka sedan på **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

4. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.


## <a name="next-steps"></a>Nästa steg
Du har nu pausas och återupptas beräkning för ditt informationslager. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.

> [!div class="nextstepaction"]
>[Läsa in data i ett SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
