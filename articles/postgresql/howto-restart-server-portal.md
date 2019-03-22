---
title: Starta om Azure Database for PostgreSQL-server med hjälp av Azure portal
description: Den här artikeln beskrivs hur du kan starta om en Azure Database for PostgreSQL-server med Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: 83947571ed835c53c6cf8da6e73deb8dceabdd62
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58168783"
---
# <a name="restart-azure-database-for-postgresql-server-using-the-azure-portal"></a>Starta om Azure Database for PostgreSQL-server med Azure portal
Det här avsnittet beskrivs hur du kan starta om en Azure Database for PostgreSQL-server. Du kan behöva starta om servern för underhåll orsaker, vilket medför ett kort avbrott som servern utför åtgärden.

Starta om servern kommer att blockeras om tjänsten är upptagen. Tjänsten kan till exempel behandlar tidigare åtgärden, till exempel skala virtuella kärnor.
 
Den tid som krävs för att slutföra en omstart är beroende av PostgreSQL återställningsprocessen. Om du vill minska tiden för omstart, rekommenderar vi du minska mängden aktiviteten på server före omstarten.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här guiden:
- En [Azure Database for PostgreSQL-server och databas](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Utföra omstart av servern

Starta följande steg om PostgreSQL-server:

1. I den [Azure-portalen](https://portal.azure.com/), Välj din Azure Database for PostgreSQL-server.

2. I verktygsfältet på serverns **översikt** klickar du på **starta om**.

   ![Azure Database för PostgreSQL – översikt – starta](./media/howto-restart-server-portal/2-server.png)

3. Klicka på **Ja** att bekräfta att starta om servern.

   ![Azure Database för PostgreSQL – omstart bekräfta](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observera att Serverstatusen ändras till ”startar om”.

   ![Azure Database för PostgreSQL – omstart status](./media/howto-restart-server-portal/4-restarting-status.png)

5. Bekräfta omstart av servern är klar.

   ![Azure Database för PostgreSQL – omstart lyckades](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du ställer in parametrarna i Azure Database för PostgreSQL](howto-configure-server-parameters-using-portal.md)