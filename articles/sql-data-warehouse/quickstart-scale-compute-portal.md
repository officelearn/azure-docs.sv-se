---
title: Skala beräkning för Synapse SQL-pool (Azure Portal)
description: Du kan skala beräkning för Synapse SQL-pool (informations lager) med hjälp av Azure Portal.
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
ms.openlocfilehash: df0b21e98812faf99b6e67f262cec6e9c29db2f1
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130257"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Snabb start: skala beräkning för Synapse SQL-pool med Azure Portal

Du kan skala beräkning för Synapse SQL-pool (informations lager) med hjälp av Azure Portal. [Skala ut beräkning](sql-data-warehouse-manage-compute-overview.md) för bättre prestanda eller skala ned beräkning om du vill sänka kostnaderna. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Du kan skala en SQL-pool som du redan har eller använda [snabb start: skapa och ansluta – Portal](create-data-warehouse-portal.md) för att skapa en SQL-pool med namnet **mySampleDataWarehouse**. I den här snabbstarten skalas **mySampleDataWarehouse**.

>[!IMPORTANT] 
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
Om du vill veta mer om SQL-poolen fortsätter du till självstudien [Läs in data i SQL-pool](load-data-from-azure-blob-storage-using-polybase.md) . 
