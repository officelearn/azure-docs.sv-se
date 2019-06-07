---
title: Auto-väx lagring i Azure Database for MariaDB med Azure portal
description: Den här artikeln beskrivs hur du kan aktivera automatisk utöka lagringen för Azure Database for MariaDB med hjälp av Azure portal
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: bb3291b66776a5f0f6be16069b2d6a999b2d1f32
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676885"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Auto-väx lagring i Azure Database for MariaDB med Azure portal
Den här artikeln beskrivs hur du kan konfigurera en Azure Database for MariaDB-serverlagring att växa utan att påverka arbetsbelastningen.

När en server når gränsen allokerat lagringsutrymme, har servern markerats som skrivskyddad. Men om du aktiverar storage auto väx ökar serverlagring för att tillgodose det växande data. För servrar med färre än 100 GB etablerad lagring ökar den allokerade lagringsstorleken med 5 GB när det lediga lagringsutrymmet som understiger det större av 1 GB eller 10% av allokerat lagringsutrymme. För servrar med fler än 100 GB allokerat lagringsutrymme ökar den allokerade lagringsstorleken med 5% när det lediga utrymmet är mindre än 5% av den allokerade lagringsstorleken. Maximalt lagringsutrymme gränser som anges [här](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) gäller.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du behöver följande för att slutföra den här guiden:
- En [Azure Database for MariaDB-server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera storage automatiskt växa 

Följ dessa steg om du vill ange MariaDB server storage auto väx:

1. I den [Azure-portalen](https://portal.azure.com/), Välj din Azure Database for MariaDB-server.

2. På sidan MariaDB under **inställningar** klickar **prisnivå** att öppna sidan med priser nivå.

3. Markera under automatisk tillväxt **Ja** för att aktivera storage automatiskt växa.

    ![Azure Database for MariaDB - Settings_Pricing_tier - Auto-growth](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klicka på **OK** för att spara ändringarna.

5. Ett meddelande bekräftar att automatiskt växa har aktiverats.

    ![Azure Database for MariaDB - automatisk tillväxt lyckades](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [så skapa aviseringar för mått](howto-alert-metric.md).
