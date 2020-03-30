---
title: Ta bort ett undernät när du har tagit bort en hanterad instans
description: Lär dig hur du tar bort ett virtuellt Azure-nätverk när du har tagit bort en hanterad Azure SQL-databas-hanterad instans.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820469"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Ta bort ett undernät efter att ha tagit bort en hanterad Azure SQL-databas-hanterad instans

Den här artikeln innehåller riktlinjer för hur du manuellt tar bort ett undernät efter att du har tagit bort den senaste Azure SQL-databashanterade instansen som finns i det.

Hanterade instanser distribueras till [virtuella kluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture). Varje virtuellt kluster är associerat med ett undernät. Det virtuella klustret finns kvar avsiktligt i 12 timmar efter borttagningen av den sista instansen så att du snabbare kan skapa hanterade instanser i samma undernät. Det finns ingen avgift för att behålla ett tomt virtuellt kluster. Det undernät som är associerat med det virtuella klustret kan inte tas bort under denna period.

Om du inte vill vänta 12 timmar och föredrar att ta bort det virtuella klustret och dess undernät tidigare kan du göra det manuellt. Ta bort det virtuella klustret manuellt med hjälp av Azure-portalen eller API:et för virtuella kluster.

> [!IMPORTANT]
> - Det virtuella klustret bör inte innehålla några hanterade instanser för att borttagningen ska lyckas. 
> - Borttagning av ett virtuellt kluster är en tidskrävande åtgärd som varar i ca 1,5 timmar (se [Hanterade instanshanteringsåtgärder](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) för aktuell virtuell klusterborttagningstid) under vilken det virtuella klustret fortfarande kommer att vara synligt i portalen tills den här processen är klar.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Ta bort virtuellt kluster från Azure-portalen

Om du vill ta bort ett virtuellt kluster med hjälp av Azure-portalen söker du efter de virtuella klusterresurserna.

![Skärmbild av Azure-portalen, med sökrutan markerad](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

När du har hittat det virtuella kluster som du vill ta bort markerar du den här resursen och väljer **Ta bort**. Du uppmanas att bekräfta borttagningen av det virtuella klustret.

![Skärmbild av instrumentpanelen för virtuella azure-portaler med alternativet Ta bort markerat](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure Portal-meddelanden visar en bekräftelse på att begäran om att ta bort det virtuella klustret har skickats. Borttagningsåtgärden i sig kommer att pågå i cirka 1,5 timmar under vilken det virtuella klustret fortfarande kommer att vara synligt i portalen. När processen är klar kommer det virtuella klustret inte längre att vara synligt och undernätet som är associerat med det kommer att släppas för återanvändning.

> [!TIP]
> Om det inte finns några hanterade instanser som visas i det virtuella klustret och du inte kan ta bort det virtuella klustret, kontrollerar du att du inte har en pågående instansdistribution pågående. Detta inkluderar startade och avbrutna distributioner som fortfarande pågår. Detta beror på att dessa åtgärder fortfarande kommer att använda det virtuella klustret som låser den från borttagning. Granska distributioner fliken för resursgruppen instansen har distribuerats till kommer att ange eventuella pågående distributioner. I det här fallet väntar du på att distributionen ska slutföras, tar bort hanterad instans och sedan det virtuella klustret.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Ta bort virtuellt kluster med hjälp av API:et

Om du vill ta bort ett virtuellt kluster via API:et använder du de URI-parametrar som anges i [metoden för borttagning av virtuella kluster](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- Lär dig mer om [anslutningsarkitektur i Hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- Lär dig hur du [ändrar ett befintligt virtuellt nätverk för hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).
- En självstudiekurs som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställer en databas från en databassäkerhetskopiering finns i [Skapa en hanterad Azure SQL-databas-hanterad instans](sql-database-managed-instance-get-started.md).
- För DNS-problem finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
