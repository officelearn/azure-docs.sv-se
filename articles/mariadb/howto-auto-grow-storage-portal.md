---
title: Lagring med automatisk tillväxt – Azure-portal - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du kan aktivera lagring med automatisk tillväxt för Azure Database för MariaDB med Azure-portal
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7ec4c7a61725fa02f00bf38048182d640666ae8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528905"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Automatiskt utöka lagringsutrymmet i Azure Database för MariaDB med Azure-portalen
I den här artikeln beskrivs hur du kan konfigurera en Azure-databas för MariaDB-serverlagring så att den växer utan att påverka arbetsbelastningen.

När en server når den allokerade lagringsgränsen markeras servern som skrivskyddad. Men om du aktiverar automatisk lagringslagring ökar serverlagringen för att hantera växande data. För servrar med mindre än 100 GB etablerad lagring ökas den etablerade lagringsstorleken med 5 GB så snart det kostnadsfria lagringsutrymmet är lägre än 1 GB eller 10 % av den etablerade lagringen. För servrar med mer än 100 GB av etablerad lagring ökas den etablerade lagringsstorleken med 5 % när det lediga lagringsutrymmet är mindre än 5 % av den etablerade lagringsstorleken. Maximala lagringsgränser som anges [här](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) gäller.

## <a name="prerequisites"></a>Krav
För att slutföra den här guiden behöver du:
- En [Azure-databas för MariaDB-server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera automatisk lagringsodling 

Så här ställer du in automatisk odling av MariaDB-serverlagring:

1. Välj [Azure portal](https://portal.azure.com/)din befintliga Azure-databas för MariaDB-server i Azure-portalen.

2. Klicka på **Prisnivå** under rubriken Inställningar på sidan MariaDB-server för att öppna prisnivåsidan under rubriken **Inställningar.**

3. I avsnittet Automatisk tillväxt väljer du **Ja** för att aktivera automatisk lagringsökning.

    ![Azure-databas för MariaDB - Settings_Pricing_tier - Automatisk tillväxt](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Spara ändringarna genom att klicka på **OK**.

5. Ett meddelande bekräftar att automatisk odling har aktiverats.

    ![Azure Database för MariaDB - framgång för automatisk tillväxt](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [skapar aviseringar om mått](howto-alert-metric.md).
