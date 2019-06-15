---
title: Auto-väx storage med hjälp av Azure-portalen i Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskrivs hur du kan aktivera automatisk växa storage med hjälp av Azure-portalen i Azure Database för PostegreSQL - enskild Server
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676765"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Auto-väx storage med hjälp av Azure-portalen i Azure Database för PostgreSQL – enskild Server
Den här artikeln beskrivs hur du kan konfigurera en Azure Database for PostgreSQL-serverlagring att växa utan att påverka arbetsbelastningen.

När en server når gränsen allokerat lagringsutrymme, har servern markerats som skrivskyddad. Men om du aktiverar storage auto väx ökar serverlagring för att tillgodose det växande data. För servrar med färre än 100 GB etablerad lagring ökar den allokerade lagringsstorleken med 5 GB när det lediga lagringsutrymmet som understiger det större av 1 GB eller 10% av allokerat lagringsutrymme. För servrar med fler än 100 GB allokerat lagringsutrymme ökar den allokerade lagringsstorleken med 5% när det lediga utrymmet är mindre än 5% av den allokerade lagringsstorleken. Maximalt lagringsutrymme gränser som anges [här](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) gäller.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du behöver följande för att slutföra den här guiden:
- En [Azure Database for PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Aktivera storage automatiskt växa 

Följ dessa steg om du vill ange PostgreSQL server storage auto väx:

1. I den [Azure-portalen](https://portal.azure.com/), Välj din Azure Database for PostgreSQL-server.

2. På sidan PostgreSQL server under **inställningar**, klickar du på **prisnivå** att öppna sidan med priser nivå.

3. I den **automatisk tillväxt** väljer **Ja** för att aktivera storage automatiskt växa.

    ![Azure Database for PostgreSQL - Settings_Pricing_tier - automatisk tillväxt](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klicka på **OK** för att spara ändringarna.

5. Ett meddelande bekräftar att automatiskt växa har aktiverats.

    ![Azure Database för PostgreSQL – automatisk tillväxt lyckades](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [så skapa aviseringar för mått](howto-alert-on-metric.md).
