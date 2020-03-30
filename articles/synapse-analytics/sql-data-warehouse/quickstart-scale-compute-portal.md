---
title: Skala beräkning för Synapse SQL-pool (Azure-portal)
description: Du kan skala beräkning för Synapse SQL-pool (informationslager) med hjälp av Azure-portalen.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f92152658b9db83740ffc2de2dc6956003849e06
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350824"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Snabbstart: Skala beräkning för Synapse SQL-pool med Azure-portalen

Du kan skala beräkning för Synapse SQL-pool (informationslager) med hjälp av Azure-portalen. [Skala ut beräkning](sql-data-warehouse-manage-compute-overview.md) för bättre prestanda eller skala tillbaka beräkning för att spara kostnader. 

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Du kan skala en SQL-pool som du redan har eller använda [Snabbstart: skapa och ansluta - portal](create-data-warehouse-portal.md) för att skapa en SQL-pool med namnet **mySampleDataWarehouse**. I den här snabbstarten skalas **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Din SQL-pool måste vara online för att skalas. 

## <a name="scale-compute"></a>Skala beräkning

BERÄKNINGSRESURSER för SQL-pool kan skalas genom att öka eller minska informationslagerenheter. I snabbstarten [Skapa och ansluta – portal] (create-data-warehouse-portal.md) skapade du **mySampleDataWarehouse** och initierade det med 400 DWU:er. Följande steg justerar DWU för **mySampleDataWarehouse**.

Så här ändrar du informationslagerenheter:

1. Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på vänster sida i Azure-portalen.
2. Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW).** SQL-poolen öppnas.
3. Klicka på **Skala**.

    ![Klicka på Skala](./media/quickstart-scale-compute-portal/click-scale.png)

2. På panelen Skala ändrar du DWU-inställningen genom att dra skjutreglaget åt vänster eller höger. Välj sedan skala.

    ![Dra skjutreglaget](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om SQL-pool fortsätter du till [inläsningsdata i självstudiekursen för SQL-pool.](load-data-from-azure-blob-storage-using-polybase.md) 
