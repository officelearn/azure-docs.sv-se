---
title: Utöka lagringen automatiskt i Azure Database for MySQL att använda Azure Portal
description: I den här artikeln beskrivs hur du kan aktivera lagring med automatisk storleks ökning för Azure Database for MySQL med Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 864bfaefba783d93e795e8780cc02dcf991e38f1
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142031"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Utöka lagringen automatiskt i Azure Database for MySQL att använda Azure Portal
I den här artikeln beskrivs hur du kan konfigurera en Azure Database for MySQL Server lagring så att den växer utan att arbets belastningen påverkas.

När en server når den tilldelade lagrings gränsen markeras servern som skrivskyddad. Men om du aktiverar automatisk storleks ökning ökar server lagringen för att rymma växande data. För servrar med mindre än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5 GB så snart det lediga lagrings utrymmet är lägre än eller lika med 1 GB eller 10% av det allokerade lagrings utrymmet. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5% när det lediga lagrings utrymmet är under 5% av den allokerade lagrings storleken. De maximala lagrings gränser som anges [här](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här guiden:
- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera automatisk storleks ökning för lagring 

Följ de här stegen för att ställa in MySQL-serverns lagrings storlek automatiskt:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL-server.

2. På sidan MySQL-server under **inställnings** rubrik klickar du på **pris nivå** för att öppna sidan pris nivå.

3. I avsnittet automatisk utökning väljer du **Ja** för att aktivera automatisk storleks ökning.

    ![Azure Database for MySQL-Settings_Pricing_tier-Auto-EXPTREND](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klicka på **OK** för att spara ändringarna.

5. Ett meddelande bekräftar att den automatiska utökningen har Aktiver ATS.

    ![Azure Database for MySQL-automatisk tillväxt lyckades](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).
