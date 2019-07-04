---
title: Ta bort ett undernät när du tar bort en Azure SQL Database managed instance | Microsoft Docs
description: Lär dig hur du tar bort ett Azure-nätverk när du tar bort en Azure SQL Database-hanterad instans.
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
ms.date: 06/26/2019
ms.openlocfilehash: 4679ecda210fa78aad4315bc6602b67dd1795ce9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427979"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Ta bort ett undernät när du tar bort en Azure SQL Database-hanterad instans

Den här artikeln innehåller riktlinjer för hur du manuellt ta bort ett undernät när du tar bort den senaste Azure SQL Database-hanterad instans som finns i den.

SQL Database använder en [virtuellt kluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) som innehåller den hanterade instansen har tagits bort. Det virtuella klustret varar i 12 timmar efter borttagningen instans, så att du kan snabbt skapa hanterade instanser i samma undernät. Det är kostnadsfritt för att hålla ett tomt virtuellt kluster. Det undernät som är associerat med det virtuella klustret kan inte tas bort under denna period.

Om du inte vill att vänta i 12 timmar och föredra att ta bort det virtuella klustret och dess undernät direkt, kan du göra det manuellt. Ta bort det virtuella klustret manuellt med hjälp av Azure-portalen eller virtuella kluster API.

> [!NOTE]
> Det virtuella klustret bör innehålla några hanterade instanser för borttagning ska lyckas.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Ta bort virtuellt kluster från Azure portal

Sök efter virtuellt kluster-resurser för att ta bort ett virtuellt kluster med hjälp av Azure portal.

![Skärmbild av Azure-portalen med sökrutan markerat](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

När du har hittat det virtuella klustret som du vill ta bort, Välj den här resursen och välj **ta bort**. Du uppmanas att bekräfta borttagningen virtuellt kluster.

![Skärmbild av Azure portal virtuella kluster instrumentpanelen med alternativet Ta bort markerade](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Området meddelanden i Azure portal visar bekräftelse på att det virtuella klustret har tagits bort. Det virtuella klustret bort Frigör omedelbart undernätet för återanvändning.

> [!TIP]
> Om inga hanterade instanser som visas i det virtuella klustret och du kan inte ta bort det virtuella klustret, kontrollerar du att du inte har en pågående distribution pågår. Detta inkluderar igång och avbrutna distributioner som fortfarande pågår. Granska distributioner fliken resursgruppen instansen har distribuerats till visar alla distributioner pågår. I det här fallet await för distributionen har slutförts, ta bort hanterad instans och sedan på det virtuella klustret.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Ta bort virtuellt kluster med hjälp av API

Ta bort ett virtuellt kluster via API: et genom att använda URI-parametrar som anges i den [virtuella kluster ta bort metoden](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans?](sql-database-managed-instance.md).
- Lär dig mer om [anslutningsarkitektur i Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Lär dig hur du [ändra ett befintligt virtuellt nätverk för hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).
- Se en självstudie som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställa en databas från en säkerhetskopia av databasen [skapa en Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
