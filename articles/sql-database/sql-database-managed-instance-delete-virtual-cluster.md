---
title: Ta bort virtuellt nätverk när du tar bort Azure SQL Database-hanterad instans | Microsoft Docs
description: Lär dig hur du tar bort virtuellt nätverk när du tar bort Azure SQL Database-hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 95d1681c9ff9981990d873a58a2d01833d690e0f
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411993"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Ta bort undernätet när du tar bort Azure SQL Database-hanterad instans

Den här artikeln innehåller riktlinjer för hur du manuellt ta bort undernätet när du tar bort den senaste Azure SQL Database-hanterad instans som finns i den.

Den [virtuellt kluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) som innehåller inneslutna det borttagna hanterad instans sparas i 12 timmar från instans borttagningen. Det virtuella klustret sparas alive avsiktligt att snabbare skapa hanterade instanser i samma undernät. Det undernät som är associerade med det virtuella klustret kan inte tas bort under denna period.

Omedelbar versionen av det undernät som används av ett tomt virtuellt kluster är möjlig genom manuell borttagning av det virtuella klustret. Borttagning av det virtuella klustret kan ske via Azure portal eller virtuella kluster API.

> [!NOTE]
> Det virtuella klustret bör innehålla några hanterade instanser för borttagning ska lyckas.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Ta bort virtuellt kluster från Azure-portalen

Sök efter virtuellt kluster-resurser med hjälp av inbyggda sökfunktionen för att ta bort ett virtuellt kluster med hjälp av Azure portal.

![Sök efter virtuellt kluster.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

När du hittar det virtuella klustret som du vill ta bort, Välj den här resursen och välj alternativet Ta bort. Du uppmanas att bekräfta borttagningen virtuellt kluster.

![Ta bort virtuellt kluster.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Bekräfta att det virtuella klustret har tagits bort tillhandahålls i meddelanden i Azure portal. Det virtuella klustret bort Frigör omedelbart undernätet för ytterligare återanvändning.

## <a name="delete-virtual-cluster-using-api"></a>Ta bort virtuellt kluster med hjälp av API

Att ta bort en virtuell kluster via API-användningen URI-parametrar som anges i den [virtuella kluster ta bort metoden](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans?](sql-database-managed-instance.md).
- Lär dig mer om [anslutningsarkitektur i Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Lär dig hur du [ändra ett befintligt virtuellt nätverk för hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).
- Se en självstudie som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställa en databas från en säkerhetskopia av databasen [skapa en Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
