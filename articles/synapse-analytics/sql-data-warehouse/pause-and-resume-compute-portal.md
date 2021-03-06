---
title: 'Snabb start: pausa och återuppta beräkning i dedikerad SQL-pool (tidigare SQL DW) via Azure Portal'
description: Använd Azure Portal för att pausa beräkning för SQL-poolen för att spara kostnader. Återuppta beräkning när du är redo att använda data lagret.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88f6782a0f81b94977b37bd8f87bf26a0d4c8a72
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457691"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-via-the-azure-portal"></a>Snabb start: pausa och återuppta beräkning i dedikerad SQL-pool (tidigare SQL DW) via Azure Portal

Du kan använda Azure Portal för att pausa och återuppta den dedikerade SQL-poolen (tidigare SQL DW) Compute-resurser. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Använd [create och Connect-Portal](../quickstart-create-sql-pool-portal.md) för att skapa en dedikerad SQL-pool med namnet **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausa beräkning

För att minska kostnaderna kan du pausa och återuppta beräknings resurser på begäran. Om du till exempel inte använder databasen under natten och på helger, kan du pausa den under dessa tider och återuppta den under dagen.
 
>[!NOTE]
>Du debiteras inte för beräknings resurser när databasen har pausats. Men du kommer att fortsätta att debiteras för lagring. 

Följ dessa steg om du vill pausa en dedikerad SQL-pool:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **dedikerad SQL-pool (tidigare SQL DW)** på den vänstra navigerings sidan i Azure Portal.
2. Välj **mySampleDataWarehouse** från sidan **dedikerad SQL-pool (tidigare SQL DW)** för att öppna SQL-poolen. 
3. Meddelande **status** är **online** på sidan **mySampleDataWarehouse** .

    ![Beräkna online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Klicka på knappen **pausa** om du vill pausa den dedikerade SQL-poolen. 
5. En bekräftelse fråga visas där du tillfrågas om du vill fortsätta. Klicka på **Ja**.
6. Vänta en stund och se sedan till att **statusen** **pausas**.

    ![Skärm bild som visar Azure Portal för ett exempel informations lager med statusvärdet Pause.](./media/pause-and-resume-compute-portal/pausing.png)

7. När paus åtgärden är klar **pausas** statusen och alternativ knappen är **återuppta**.
8. Beräknings resurserna för den dedikerade SQL-poolen är nu offline. Du debiteras inte för beräkning förrän du återupptar tjänsten.

    ![Beräkna offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Återuppta beräkning

Följ dessa steg om du vill återuppta en dedikerad SQL-pool.

1. Klicka på **dedikerad SQL-pool (tidigare SQL DW)** på den vänstra sidan i Azure Portal.
2. Öppna sidan SQL-pool genom att välja **mySampleDataWarehouse** från den **dedikerade SQL-poolen (tidigare SQL DW))** . 
3. På sidan **mySampleDataWarehouse** har meddelande **status** **pausats**.

    ![Beräkna offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Klicka på **Fortsätt** om du vill återuppta SQL-poolen. 
1. En bekräftelse fråga visas som frågar om du vill starta. Klicka på **Ja**.
1. Observera att **statusen** **återupptas**.

    ![Skärm bild som visar Azure Portal för ett exempel informations lager med start-knappen vald och ett status värde för att återuppta.](./media/pause-and-resume-compute-portal/resuming.png)

1. När SQL-poolen är online igen är statusen **online** och alternativ knappen är **pausad**.
1. Beräknings resurserna för SQL-poolen är nu online och du kan använda tjänsten. Avgifter för beräkning har återupptagits.

    ![Beräkna online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för data lager enheter och de data som lagras i din dedikerade SQL-pool. Dessa beräknings- och lagringsresurser debiteras separat. 

- Om du vill behålla data i lagringen ska du pausa beräkningen.
- Om du vill ta bort framtida avgifter kan du ta bort den dedikerade SQL-poolen. 

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj din dedikerade SQL-pool.

    ![Rensa resurser](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. 

1. Om du vill ta bort den dedikerade SQL-poolen så att du inte debiteras för beräkning eller lagring klickar du på **ta bort**.



## <a name="next-steps"></a>Nästa steg

Du har nu pausat och återupptagit beräkningen för din dedikerade SQL-pool. Fortsätt till nästa artikel om du vill lära dig mer om hur du [läser in data i en dedikerad SQL-pool](load-data-from-azure-blob-storage-using-polybase.md). Mer information om hur du hanterar Compute-funktioner finns i artikeln [hantera beräknings översikt](sql-data-warehouse-manage-compute-overview.md) . 

