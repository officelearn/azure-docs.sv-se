---
title: "Snabbstart: Skala ut beräkning i Azure SQL Data Warehouse - Azure-portalen | Microsoft Docs"
description: "Azure portal uppgifter för hantering av beräkningskraft. Skala beräkningsresurser genom att justera dwu: er. Eller, pausa och återuppta beräkningsresurser för att spara kostnader."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: 6b86042ed6b95ba49fa2089ba36b1dbe9a61cc40
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Snabbstart: Skala compute i Azure SQL Data Warehouse i Azure-portalen

Skala bearbetning i Azure SQL Data Warehouse i Azure-portalen. [Skala ut beräkning](sql-data-warehouse-manage-compute-overview.md) tillbaka beräkning för att spara kostnader för bättre prestanda eller skalning. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Du kan skala ett datalager som du redan har, eller Använd [Snabbstart: skapa och ansluta - portal](create-data-warehouse-portal.md) att skapa ett data warehouse med namnet **mySampleDataWarehouse**.  Denna Snabbstart skalas **mySampleDataWarehouse**.

## <a name="scale-compute"></a>Skala beräkning

Du kan öka eller minska beräkningsresurser genom att justera informationslagerenheter i SQL Data Warehouse. Den [skapa och Connect - portal](create-data-warehouse-portal.md) skapade **mySampleDataWarehouse** och initieras med 400 dwu: er. Följande steg justera dwu: er för **mySampleDataWarehouse**.

Ändra informationslagerenheter:

1. Klicka på **SQL-databaser** i den vänstra sidan av Azure-portalen.
2. Välj **mySampleDataWarehouse** från den **SQL-databaser** sidan. Datalagret öppnas.
3. Klicka på **skala**.

    ![Klicka på skalan](media/quickstart-scale-compute-portal/click-scale.png)

2. Flytta skjutreglaget åt vänster eller höger för att ändra DWU-inställningen på panelen skala.

    ![Flytta skjutreglaget](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Klicka på **Spara**. Ett meddelande visas. Klicka på **Ja** att bekräfta eller **inga** att avbryta.

    ![Klicka på Spara](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig hur du kan skala beräkning för ditt informationslager. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.

> [!div class="nextstepaction"]
>[Läsa in data i ett SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
