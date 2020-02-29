---
title: 'Snabb start: skala beräkning-Azure Portal '
description: Skala beräkning i SQL-poolen i Azure Portal. Skala ut beräkning för bättre prestanda eller skala ned beräkning om du vill sänka kostnaderna.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7463849223fdf81466237c7d0c912763988e80e6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200367"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>Snabb start: skala beräkning i Azure Synapse Analytics SQL-poolen i Azure Portal

Skala beräkning i SQL-poolen i Azure Portal. [Skala ut beräkning](sql-data-warehouse-manage-compute-overview.md) för bättre prestanda eller skala ned beräkning om du vill sänka kostnaderna. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Du kan skala en SQL-pool som du redan har eller använda [snabb start: skapa och ansluta – Portal](create-data-warehouse-portal.md) för att skapa en SQL-pool med namnet **mySampleDataWarehouse**.  I den här snabbstarten skalas **mySampleDataWarehouse**.

>[!Note]
>SQL-poolen måste vara online för att kunna skalas. 

## <a name="scale-compute"></a>Skala beräkning

Beräknings resurser för SQL-pool kan skalas genom att öka eller minska informations lager enheter. I snabbstarten [Skapa och ansluta – portal] (create-data-warehouse-portal.md) skapade du **mySampleDataWarehouse** och initierade det med 400 DWU:er. Följande steg justerar DWU för **mySampleDataWarehouse**.

Så här ändrar du informationslagerenheter:

1. Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på den vänstra sidan i Azure Portal.
2. Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW)** . SQL-poolen öppnas.
3. Klicka på **Skala**.

    ![Klicka på Skala](media/quickstart-scale-compute-portal/click-scale.png)

2. På panelen Skala ändrar du DWU-inställningen genom att dra skjutreglaget åt vänster eller höger. Välj sedan skala.

    ![Dra skjutreglaget](media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig att skala beräkning för SQL-poolen. Om du vill veta mer om SQL-poolen fortsätter du till självstudien för att läsa in data.

> [!div class="nextstepaction"]
>[Läs in data i SQL-pool](load-data-from-azure-blob-storage-using-polybase.md)
