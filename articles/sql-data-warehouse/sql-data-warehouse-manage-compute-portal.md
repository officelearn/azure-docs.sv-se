---
title: Hantera datorkraft i Azure SQL Data Warehouse (Azure portal) | Microsoft Docs
description: "Azure portal uppgifter för hantering av beräkningskraft. Skala beräkningsresurser genom att justera dwu: er. Eller, pausa och återuppta beräkningsresurser för att spara kostnader."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: f56e62576cae0c594f26bcddf44528032bd5ea69
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Hantera datorkraft i Azure SQL Data Warehouse (Azure portal)
Skala beräkningsresurser i Azure SQL Data Warehouse med hjälp av Azure portal.

## <a name="scale-compute-power"></a>Skala datorkraft
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Ändra beräkningsresurser:

1. Öppna den [Azure-portalen][Azure portal], öppna databasen och på **skala**.

    ![Klicka på skalan][1]
2. Flytta skjutreglaget åt vänster eller höger för att ändra DWU-inställningen i bladet skala.

    ![Flytta skjutreglaget][2]
3. Klicka på **Spara**. Ett meddelande visas. Klicka på **Ja** att bekräfta eller **inga** att avbryta.

    ![Klicka på Spara][3]


## <a name="next-steps"></a>Nästa steg
Mer information finns i [översikt över][Management overview].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png


<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
