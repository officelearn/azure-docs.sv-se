---
title: Ta bort ett undernät efter borttagning av en Azure SQL Database Hanterad instans
description: Lär dig hur du tar bort ett virtuellt Azure-nätverk när du har tagit bort en Azure SQL Database Hanterad instans.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 84298e49de2de2ac43c33ec38c22846fd8d1e968
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688134"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Ta bort ett undernät efter borttagning av en Azure SQL Database Hanterad instans

Den här artikeln innehåller rikt linjer för hur du manuellt tar bort ett undernät när du har tagit bort den senaste Azure SQL Database hanterade instansen i det.

Hanterade instanser distribueras till [virtuella kluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture). Varje virtuellt kluster är associerat med ett undernät. Det virtuella klustret behålls av design i 12 timmar efter den sista instansen av borttagning så att du snabbt kan skapa hanterade instanser i samma undernät. Det kostar inget att hålla ett tomt virtuellt kluster. Det undernät som är associerat med det virtuella klustret kan inte tas bort under denna period.

Om du inte vill vänta i 12 timmar och vill ta bort det virtuella klustret och dess undernät tidigare kan du göra det manuellt. Ta bort det virtuella klustret manuellt med hjälp av Azure Portal eller det virtuella kluster-API: et.

> [!IMPORTANT]
> - Det virtuella klustret ska inte innehålla några hanterade instanser för att borttagningen ska lyckas. 
> - Borttagning av ett virtuellt kluster är en tids krävande åtgärd som varar i cirka 1,5 timmar (se [hanterings åtgärder för hanterade instanser](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) för upp till det virtuella kluster borttagnings tiden) under vilken det virtuella klustret fortfarande visas i portalen tills detta processen har slutförts.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Ta bort virtuellt kluster från Azure Portal

Om du vill ta bort ett virtuellt kluster med hjälp av Azure Portal söker du efter de virtuella kluster resurserna.

![Skärm bild av Azure Portal, där sökrutan är markerad](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

När du har hittat det virtuella kluster som du vill ta bort väljer du den här resursen och väljer **ta bort**. Du uppmanas att bekräfta borttagningen av det virtuella klustret.

![Skärm bild av instrument panelen för Azure Portal virtuella kluster med alternativet ta bort markerat](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure Portal-aviseringar visar en bekräftelse på att begäran om att ta bort det virtuella klustret har skickats. Borttagnings åtgärden kommer att räcka för cirka 1,5 timmar då det virtuella klustret fortfarande visas i portalen. När processen har slutförts visas inte längre det virtuella klustret och det undernät som är associerat med det kommer att släppas för åter användning.

> [!TIP]
> Om det inte finns några hanterade instanser i det virtuella klustret, och du inte kan ta bort det virtuella klustret, kontrollerar du att du inte har en pågående instans distribution. Detta inkluderar startade och avbrutna distributioner som fortfarande pågår. Detta beror på att de här åtgärderna fortfarande kommer att använda det virtuella klustret som låser den från att tas bort. Fliken för att granska distributioner i resurs gruppen som instansen distribuerades till anger att alla distributioner pågår. I det här fallet väntar du tills distributionen har slutförts, tar bort den hanterade instansen och sedan det virtuella klustret.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Ta bort virtuellt kluster med hjälp av API: et

Om du vill ta bort ett virtuellt kluster via API: et använder du URI-parametrarna som anges i [Delete-metoden för virtuella kluster](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- Lär dig mer om [anslutnings arkitekturen i en hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- Lär dig hur du [ändrar ett befintligt virtuellt nätverk för en hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).
- En själv studie kurs som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställer en databas från en säkerhets kopia av databasen finns i [skapa en Azure SQL Database Hanterad instans](sql-database-managed-instance-get-started.md).
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
