---
title: 'Snabbstart: Pausa och återuppta beräkning i Azure SQL Data Warehouse-Azure Portal | Microsoft Docs'
description: Använd Azure Portal för att pausa beräknings Azure SQL Data Warehouse för att spara kostnader. Återuppta beräkning när du är redo att använda data lagret.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 46fb1fc6455cd35d8bb1eaf0a1b7ee499f5c1caf
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574885"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Snabbstart: Pausa och återuppta beräkning för en Azure SQL Data Warehouse i Azure Portal

Använd Azure Portal för att pausa beräknings Azure SQL Data Warehouse för att spara kostnader. [Återuppta beräkning](sql-data-warehouse-manage-compute-overview.md) när du är redo att använda data lagret.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Använd [skapa och Anslut – Portal](create-data-warehouse-portal.md) för att skapa ett informations lager med namnet **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausa beräkning

För att spara kostnader kan du pausa och återuppta beräknings resurser på begäran. Om du till exempel inte använder databasen under natten och på helger, kan du pausa den under dessa tider och återuppta den under dagen. Du debiteras inte för beräknings resurser när databasen har pausats. Men du kommer att fortsätta att debiteras för lagring. 

Följ dessa steg om du vill pausa en SQL Data Warehouse.

1. Klicka på **SQL-databaser** på den vänstra sidan i Azure-portalen.
2. Välj **mySampleDataWarehouse** på sidan **SQL-databaser**. Informationslagret öppnas. 
3. Meddelande **status** är **online**på sidan **mySampleDataWarehouse** .

    ![Beräkna online](media/pause-and-resume-compute-portal/compute-online.png)

4. Klicka på knappen **pausa** om du vill pausa data lagret. 
5. En bekräftelse fråga visas där du tillfrågas om du vill fortsätta. Klicka på **Ja**.
6. Vänta en stund och se sedan till att **statusen** pausas.

    ![Pausar](media/pause-and-resume-compute-portal/pausing.png)

7. När paus åtgärden är klar pausas statusen och alternativ knappen **startas**.
8. Beräknings resurserna för data lagret är nu offline. Du debiteras inte för beräkning förrän du återupptar tjänsten.

    ![Beräkna offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Återuppta beräkning

Följ de här stegen för att återuppta en SQL Data Warehouse.

1. Klicka på **SQL-databaser** på den vänstra sidan i Azure-portalen.
2. Välj **mySampleDataWarehouse** på sidan **SQL-databaser**. Informationslagret öppnas. 
3. På sidan **mySampleDataWarehouse** har meddelande **status** pausats.

    ![Beräkna offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Klicka på **Starta**om du vill återuppta data lagret. 
5. En bekräftelse fråga visas som frågar om du vill starta. Klicka på **Ja**.
6. Observera att **statusen** **återupptas**.

    ![Återupptar](media/pause-and-resume-compute-portal/resuming.png)

7. När data lagret är online igen är statusen **online** och alternativ knappen är **pausad**.
8. Beräknings resurserna för data lagret är nu online och du kan använda tjänsten. Avgifter för beräkning har återupptagits.

    ![Beräkna online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för data lager enheter och de data som lagras i ditt informations lager. Dessa beräknings- och lagringsresurser debiteras separat. 

- Om du vill behålla data i lagringen ska du pausa beräkningen.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret. 

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure Portal](https://portal.azure.com)och klicka på ditt informations lager.

    ![Rensa resurser](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. När informationslagret har pausats visas knappen **Starta**.  Klicka på **Starta** om du vill återuppta beräkningarna.

2. Om du vill ta bort informationslagret så att du varken debiteras för beräkning eller lagring klickar du på **Ta bort**.

3. Om du vill ta bort den SQL-Server som du har skapat klickar du på **mynewserver-20171113.Database.Windows.net**och sedan på **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

4. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.


## <a name="next-steps"></a>Nästa steg

Du har nu pausat och återupptagit beräkningen för ditt informations lager. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.

> [!div class="nextstepaction"]
> [Läs in data i en SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
