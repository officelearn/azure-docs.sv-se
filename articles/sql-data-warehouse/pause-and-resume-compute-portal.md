---
title: Pausa och återuppta beräkning i Synapse SQL-poolen via Azure Portal
description: Använd Azure Portal för att pausa beräkning för SQL-poolen för att spara kostnader. Återuppta beräkning när du är redo att använda data lagret.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3a131c1ebbf2a69f1c738cbc3421635406500d3c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130363"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Snabb start: pausa och återuppta beräkning i Synapse SQL-pool via Azure Portal

Du kan använda Azure Portal för att pausa och återuppta beräknings resurserna för Synapse SQL-poolen (data lagret). Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Använd [skapa och Anslut – portalen](create-data-warehouse-portal.md) för att skapa en SQL-pool med namnet **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausa beräkning

För att minska kostnaderna kan du pausa och återuppta beräknings resurser på begäran. Om du till exempel inte använder databasen under natten och på helger, kan du pausa den under dessa tider och återuppta den under dagen. 
>[!NOTE]
>Du debiteras inte för beräknings resurser när databasen har pausats. Men du kommer att fortsätta att debiteras för lagring. 

Följ dessa steg om du vill pausa en SQL-pool:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på den vänstra navigerings sidan i Azure Portal.
2. Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW)** för att öppna SQL-poolen. 
3. Meddelande **status** är **online**på sidan **mySampleDataWarehouse** .

    ![Beräkna online](media/pause-and-resume-compute-portal/compute-online.png)

4. Klicka på knappen **pausa** om du vill pausa SQL-poolen. 
5. En bekräftelse fråga visas där du tillfrågas om du vill fortsätta. Klicka på **Ja**.
6. Vänta en stund och se sedan till att **statusen** **pausas**.

    ![Gör](media/pause-and-resume-compute-portal/pausing.png)

7. När paus åtgärden är klar **pausas** statusen och alternativ knappen är **återuppta**.
8. Beräknings resurserna för SQL-poolen är nu offline. Du debiteras inte för beräkning förrän du återupptar tjänsten.

    ![Beräkna offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Återuppta beräkning

Följ de här stegen för att återuppta SQL-poolen.

1. Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på den vänstra sidan i Azure Portal.
2. Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW)** för att öppna sidan SQL-pool. 
3. På sidan **mySampleDataWarehouse** har meddelande **status** **pausats**.

    ![Beräkna offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Klicka på **Fortsätt**om du vill återuppta SQL-poolen. 
5. En bekräftelse fråga visas som frågar om du vill starta. Klicka på **Ja**.
6. Observera att **statusen** **återupptas**.

    ![Återupptar](media/pause-and-resume-compute-portal/resuming.png)

7. När SQL-poolen är online igen är statusen **online** och alternativ knappen är **pausad**.
8. Beräknings resurserna för SQL-poolen är nu online och du kan använda tjänsten. Avgifter för beräkning har återupptagits.

    ![Beräkna online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för data lager enheter och de data som lagras i SQL-poolen. Dessa beräknings- och lagringsresurser debiteras separat. 

- Om du vill behålla data i lagringen ska du pausa beräkningen.
- Om du vill ta bort framtida avgifter kan du ta bort SQL-poolen. 

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure Portal](https://portal.azure.com)och klicka på SQL-poolen.

    ![Rensa resurser](media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. 

2. Om du vill ta bort SQL-poolen så att du inte debiteras för beräkning eller lagring klickar du på **ta bort**.

3. Om du vill ta bort den SQL-Server som du har skapat klickar du på **sqlpoolservername.Database.Windows.net**och sedan på **ta bort**.  

   > [!CAUTION]
   > Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.


## <a name="next-steps"></a>Nästa steg

Du har nu pausat och återupptagit beräkningen för SQL-poolen. Fortsätt till nästa artikel om du vill lära dig mer om hur du [läser in data i SQL-poolen](load-data-from-azure-blob-storage-using-polybase.md). Mer information om hur du hanterar Compute-funktioner finns i artikeln [hantera beräknings översikt](sql-data-warehouse-manage-compute-overview.md) . 

