---
title: Starta om Azure Database for PostgreSQL-server med hjälp av Azure portal
description: Den här artikeln beskrivs hur du kan starta om en Azure Database for PostgreSQL-server med Azure Portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 28e99f64fdee414549c55f9666bfd53f07fb3efb
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892702"
---
# <a name="restart-azure-database-for-postgresql-server-using-azure-portal"></a>Starta om Azure Database for PostgreSQL-server med hjälp av Azure portal
Det här avsnittet beskrivs hur du kan starta om en Azure Database for PostgreSQL-server. Du kan behöva starta om servern för underhåll orsaker, vilket medför ett kort avbrott som servern utför åtgärden.

Starta om servern kommer att blockeras om tjänsten är upptagen. Tjänsten kan till exempel behandlar tidigare åtgärden, till exempel skala virtuella kärnor.
 
Den tid som krävs för att slutföra en omstart är beroende av PostgreSQL återställningsprocessen. Om du vill minska tiden för omstart, rekommenderar vi du minska mängden aktiviteten på server före omstarten.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här guiden:
- En [Azure Database for PostgreSQL-server och databas](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Utföra omstart av servern

Starta följande steg om PostgreSQL-server:

1. Välj din Azure Database for PostgreSQL-server i Azure-portalen.

2. I verktygsfältet på serverns **översikt** klickar du på **starta om**.

   ![Azure Database för PostgreSQL – översikt – starta](./media/howto-restart-server-portal/2-server.png)

3. Klicka på **Ja** att bekräfta att starta om servern.

   ![Azure Database för PostgreSQL – omstart bekräfta ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observera att Serverstatusen ändras till ”startar om”.

   ![Azure Database för PostgreSQL – omstart status ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Bekräfta omstart av servern är klar.

   ![Azure Database för PostgreSQL – omstart lyckades ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Skapa Azure Database for PostgreSQL-server med Azure-portalen](./quickstart-create-server-database-portal.md)