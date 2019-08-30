---
title: Utöka lagringen automatiskt med hjälp av Azure Portal i Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du kan aktivera automatisk storleks ökning med hjälp av Azure Portal i Azure Database for PostgreSQL-enskild server
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 1d028093b030e1f2cf00ceae9297563c36c314c5
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142880"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Utöka lagringen automatiskt med hjälp av Azure Portal i Azure Database for PostgreSQL-enskild server
I den här artikeln beskrivs hur du kan konfigurera en Azure Database for PostgreSQL Server lagring så att den växer utan att arbets belastningen påverkas.

När en server når den tilldelade lagrings gränsen markeras servern som skrivskyddad. Men om du aktiverar automatisk storleks ökning ökar server lagringen för att rymma växande data. För servrar med mindre än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5 GB så snart det lediga lagrings utrymmet är lägre än eller lika med 1 GB eller 10% av det allokerade lagrings utrymmet. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5% när det lediga lagrings utrymmet är under 5% av den allokerade lagrings storleken. De maximala lagrings gränser som anges [här](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här guiden:
- En [Azure Database for postgresql-server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera automatisk storleks ökning för lagring 

Följ de här stegen för att ställa in PostgreSQL Server Storage Auto:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for postgresql-server.

2. På sidan PostgreSQL Server under **Inställningar**klickar du på **pris nivå** för att öppna sidan pris nivå.

3. I avsnittet **automatisk utökning** väljer du **Ja** för att aktivera automatisk storleks ökning.

    ![Azure Database for PostgreSQL-Settings_Pricing_tier-Auto-EXPTREND](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klicka på **OK** för att spara ändringarna.

5. Ett meddelande bekräftar att den automatiska utökningen har Aktiver ATS.

    ![Azure Database for PostgreSQL-automatisk tillväxt lyckades](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).
