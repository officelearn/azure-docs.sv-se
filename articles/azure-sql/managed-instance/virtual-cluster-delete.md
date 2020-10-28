---
title: Ta bort ett undernät när en hanterad instans av SQL-hanterad instans har tagits bort
description: Lär dig hur du tar bort ett virtuellt Azure-nätverk när du har tagit bort en hanterad instans av Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 4ed8f6dc90debddd17282f8f96962ffd78055030
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791672"
---
# <a name="delete-a-subnet-after-deleting-a-managed-instance-of-sql-managed-instance"></a>Ta bort ett undernät när en hanterad instans av SQL-hanterad instans har tagits bort
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln innehåller rikt linjer för hur du manuellt tar bort ett undernät efter att du tagit bort den senaste hanterade instansen av Azure SQL-hanterad instans i det.

Hanterade instanser distribueras till [virtuella kluster](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Varje virtuellt kluster är associerat med ett undernät. Det virtuella klustret behålls av design i 12 timmar efter den sista instansen av borttagning så att du snabbt kan skapa hanterade instanser i samma undernät. Det kostar inget att hålla ett tomt virtuellt kluster. Det undernät som är associerat med det virtuella klustret kan inte tas bort under denna period.

Om du inte vill vänta i 12 timmar och vill ta bort det virtuella klustret och dess undernät tidigare kan du göra det manuellt. Ta bort det virtuella klustret manuellt med hjälp av Azure Portal eller det virtuella kluster-API: et.

> [!IMPORTANT]
> - Det virtuella klustret ska inte innehålla några hanterade instanser för att borttagningen ska lyckas. 
> - Borttagning av ett virtuellt kluster är en tids krävande åtgärd som varar i cirka 1,5 timmar (se [hanterings åtgärder för hanterade instanser](./sql-managed-instance-paas-overview.md#management-operations) för uppdaterade virtuella kluster borttagnings tider). Det virtuella klustret visas fortfarande i portalen tills den här processen har slutförts.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Ta bort ett virtuellt kluster från Azure Portal

Om du vill ta bort ett virtuellt kluster med hjälp av Azure Portal söker du efter de virtuella kluster resurserna.

![Skärm bild av Azure Portal, där sökrutan är markerad](./media/virtual-cluster-delete/virtual-clusters-search.png)

När du har hittat det virtuella kluster som du vill ta bort väljer du den här resursen och väljer **ta bort** . Du uppmanas att bekräfta borttagningen av det virtuella klustret.

![Skärm bild av instrument panelen för Azure Portal virtuella kluster med alternativet ta bort markerat](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure Portal-aviseringar visar en bekräftelse på att begäran om att ta bort det virtuella klustret har skickats. Själva borttagnings åtgärden kommer att vara sist i cirka 1,5 timmar, då det virtuella klustret fortfarande visas i portalen. När processen har slutförts visas inte längre det virtuella klustret och det undernät som är associerat med det kommer att släppas för åter användning.

> [!TIP]
> Om det inte finns några hanterade instanser i det virtuella klustret, och du inte kan ta bort det virtuella klustret, kontrollerar du att du inte har en pågående instans distribution. Detta inkluderar startade och avbrutna distributioner som fortfarande pågår. Detta beror på att de här åtgärderna fortfarande använder det virtuella klustret och låser det från att tas bort. Granska fliken **distributioner** i resurs gruppen som instansen distribuerades till anger att alla distributioner pågår. I det här fallet väntar du tills distributionen har slutförts, tar bort den hanterade instansen och tar sedan bort det virtuella klustret.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Ta bort ett virtuellt kluster med hjälp av API: et

Om du vill ta bort ett virtuellt kluster via API: et använder du URI-parametrarna som anges i [Delete-metoden för virtuella kluster](/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en Azure SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
- Lär dig mer om [anslutnings arkitektur i SQL-hanterad instans](connectivity-architecture-overview.md).
- Lär dig hur du [ändrar ett befintligt virtuellt nätverk för SQL-hanterad instans](vnet-existing-add-subnet.md).
- En själv studie kurs som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställer en databas från en säkerhets kopia av databasen finns i [skapa en hanterad instans](instance-create-quickstart.md).
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](custom-dns-configure.md).