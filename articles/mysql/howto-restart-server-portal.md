---
title: Starta om Server – Azure Portal-Azure Database for MySQL
description: I den här artikeln beskrivs hur du kan starta om en Azure Database for MySQL-server med hjälp av Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 1857c67a77b9600f3d8f7c222a8e06f899fba728
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774059"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Starta om Azure Database for MySQL server med Azure Portal
I det här avsnittet beskrivs hur du kan starta om en Azure Database for MySQL-server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott eftersom servern utför åtgärden.

Servern kommer att startas om när tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.

Tiden som krävs för att slutföra en omstart beror på MySQL-återställnings processen. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om.

## <a name="prerequisites"></a>Krav
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Utför omstart av Server

Följande steg startar om MySQL-servern:

1. I Azure Portal väljer du Azure Database for MySQL-servern.

2. I verktygsfältet på serverns **översikts** sida klickar du på **starta om**.

   ![Azure Database for MySQL-översikt – knappen starta om](./media/howto-restart-server-portal/2-server.png)

3. Bekräfta att du vill starta om servern genom att klicka på **Ja** .

   ![Bekräfta Azure Database for MySQL omstart](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observera att Server statusen ändras till "omstart".

   ![Azure Database for MySQL-restart-status](./media/howto-restart-server-portal/4-restarting-status.png)

5. Bekräfta att servern har startats om.

   ![Azure Database for MySQL-omstart lyckades](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Nästa steg

[Snabb start: Skapa Azure Database for MySQL server med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
