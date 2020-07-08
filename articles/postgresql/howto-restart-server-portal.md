---
title: Starta om Server-Azure Portal-Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs hur du kan starta om en Azure Database for PostgreSQL-enskild server med hjälp av Azure Portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74770092"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Starta om Azure Database for PostgreSQL – en server med hjälp av Azure Portal
I det här avsnittet beskrivs hur du kan starta om en Azure Database for PostgreSQL-Server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott eftersom servern utför åtgärden.

Servern kommer att startas om när tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.
 
Tiden som krävs för att slutföra en omstart beror på återställnings processen för PostgreSQL. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om.

## <a name="prerequisites"></a>Krav
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for postgresql-server](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Utför omstart av Server

Följande steg startar om PostgreSQL-servern:

1. I [Azure Portal](https://portal.azure.com/)väljer du Azure Database for PostgreSQL-servern.

2. I verktygsfältet på serverns **översikts** sida klickar du på **starta om**.

   ![Azure Database for PostgreSQL-översikt – knappen starta om](./media/howto-restart-server-portal/2-server.png)

3. Bekräfta att du vill starta om servern genom att klicka på **Ja** .

   ![Bekräfta Azure Database for PostgreSQL omstart](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observera att Server statusen ändras till "omstart".

   ![Azure Database for PostgreSQL-restart-status](./media/howto-restart-server-portal/4-restarting-status.png)

5. Bekräfta att servern har startats om.

   ![Azure Database for PostgreSQL-omstart lyckades](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du ställer in parametrar i Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)