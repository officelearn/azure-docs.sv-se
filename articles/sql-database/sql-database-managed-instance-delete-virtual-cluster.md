---
title: Ta bort ett undernät efter att ha tagit bort en Azure SQL Database Hanterad instans | Microsoft Docs
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
ms.openlocfilehash: f6e0b55ad2fbd9b4c45dbd1facaebd4750314c63
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567535"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Ta bort ett undernät efter borttagning av en Azure SQL Database Hanterad instans

Den här artikeln innehåller rikt linjer för hur du manuellt tar bort ett undernät när du har tagit bort den senaste Azure SQL Database hanterade instansen i det.

SQL Database använder ett [virtuellt kluster](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) för att inkludera den borttagna hanterade instansen. Det virtuella klustret är kvar i 12 timmar efter att instansen har tagits bort, så att du snabbt kan skapa hanterade instanser i samma undernät. Det kostar inget att hålla ett tomt virtuellt kluster. Det undernät som är associerat med det virtuella klustret kan inte tas bort under denna period.

Om du inte vill vänta i 12 timmar och föredrar att ta bort det virtuella klustret och dess undernät omedelbart kan du göra det manuellt. Ta bort det virtuella klustret manuellt med hjälp av Azure Portal eller det virtuella kluster-API: et.

> [!NOTE]
> Det virtuella klustret ska inte innehålla några hanterade instanser för att borttagningen ska lyckas.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Ta bort virtuellt kluster från Azure Portal

Om du vill ta bort ett virtuellt kluster med hjälp av Azure Portal söker du efter de virtuella kluster resurserna.

![Skärm bild av Azure Portal, där sökrutan är markerad](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

När du har hittat det virtuella kluster som du vill ta bort väljer du den här resursen och väljer **ta bort**. Du uppmanas att bekräfta borttagningen av det virtuella klustret.

![Skärm bild av instrument panelen för Azure Portal virtuella kluster med alternativet ta bort markerat](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

I det Azure Portal meddelande fältet visas en bekräftelse på att det virtuella klustret har tagits bort. Om du tar bort det virtuella klustret omedelbart frigörs under nätet för åter användning.

> [!TIP]
> Om det inte finns några hanterade instanser i det virtuella klustret, och du inte kan ta bort det virtuella klustret, kontrollerar du att du inte har en pågående instans distribution. Detta inkluderar startade och avbrutna distributioner som fortfarande pågår. Fliken för att granska distributioner i resurs gruppen som instansen distribuerades till anger att alla distributioner pågår. I det här fallet väntar du på att distributionen ska slutföras, tar bort den hanterade instansen och sedan det virtuella klustret.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Ta bort virtuellt kluster med hjälp av API: et

Om du vill ta bort ett virtuellt kluster via API: et använder du URI-parametrarna som anges i [Delete-metoden för virtuella kluster](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- Lär dig mer om [anslutnings arkitekturen i en hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- Lär dig hur du [ändrar ett befintligt virtuellt nätverk för en hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).
- En själv studie kurs som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställer en databas från en säkerhets kopia av databasen finns i [skapa en Azure SQL Database Hanterad instans](sql-database-managed-instance-get-started.md).
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
