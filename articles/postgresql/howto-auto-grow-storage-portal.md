---
title: Lagring med automatisk tillväxt – Azure-portal - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du kan konfigurera lagring automatiskt med hjälp av Azure-portalen i Azure Database for PostgreSQL - Single Server
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5e4f9d68d02edf456394d4ce10b7b6af5f8643d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769242"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Utöka lagring automatiskt med Azure-portalen i Azure Database for PostgreSQL - Single Server
I den här artikeln beskrivs hur du kan konfigurera en Azure-databas för PostgreSQL-serverlagring så att den växer utan att påverka arbetsbelastningen.

När en server når den allokerade lagringsgränsen markeras servern som skrivskyddad. Men om du aktiverar automatisk lagringslagring ökar serverlagringen för att hantera växande data. För servrar med mindre än 100 GB etablerad lagring ökas den etablerade lagringsstorleken med 5 GB så snart det kostnadsfria lagringsutrymmet är lägre än 1 GB eller 10 % av den etablerade lagringen. För servrar med mer än 100 GB av etablerad lagring ökas den etablerade lagringsstorleken med 5 % när det lediga lagringsutrymmet är mindre än 5 % av den etablerade lagringsstorleken. Maximala lagringsgränser som anges [här](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) gäller.

## <a name="prerequisites"></a>Krav
För att slutföra den här guiden behöver du:
- En [Azure-databas för PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera automatisk lagringsodling 

Så här ställer du in automatisk lagring av PostgreSQL-serverlagring:

1. Välj [Azure portal](https://portal.azure.com/)din befintliga Azure-databas för PostgreSQL-server i Azure-portalen.

2. Klicka på **Prisnivå** under Inställningar på sidan PostgreSQL-server för att öppna prisnivåsidan under **Inställningar.**

3. I avsnittet **Automatisk tillväxt** väljer du **Ja** för att aktivera automatisk lagringsökning.

    ![Azure-databas för PostgreSQL - Settings_Pricing_tier - Automatisk tillväxt](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Spara ändringarna genom att klicka på **OK**.

5. Ett meddelande bekräftar att automatisk odling har aktiverats.

    ![Azure Database för PostgreSQL - framgång för automatisk tillväxt](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [skapar aviseringar om mått](howto-alert-on-metric.md).
