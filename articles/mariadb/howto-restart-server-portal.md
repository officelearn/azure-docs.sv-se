---
title: Starta om servern - Azure portal - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du kan starta om en Azure-databas för MariaDB-server med Hjälp av Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed95304807a1a03880cc824c1a58f010203d418d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534719"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Starta om Azure Database för MariaDB-server med Azure-portalen
I det här avsnittet beskrivs hur du kan starta om en Azure-databas för MariaDB-server. Du kan behöva starta om servern av underhållsskäl, vilket medför ett kort avbrott när servern utför åtgärden.

Omstarten av servern blockeras om tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalning av virtuella kärnor.

Hur länge som krävs för att slutföra en omstart beror på MariaDB-återställningsprocessen. Om du vill minska omstartstiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern före omstarten.

## <a name="prerequisites"></a>Krav
För att slutföra den här guiden behöver du:
- En [Azure-databas för MariaDB-server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Starta om servern

Följande steg startar om MariaDB-servern:

1. Välj din Azure-databas för MariaDB-server i Azure-portalen.

2. Klicka på **Starta om**i verktygsfältet på serverns **översiktssida** .

   ![Azure Database för MariaDB - Översikt - knappen Starta om](./media/howto-restart-server-portal/2-server.png)

3. Klicka på **Ja** om du vill bekräfta att servern startas om.

   ![Azure Database för MariaDB - Starta om bekräfta](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observera att serverstatus ändras till "Starta om".

   ![Azure Database för MariaDB - Omstartsstatus](./media/howto-restart-server-portal/4-restarting-status.png)

5. Bekräfta att omstarten av servern har slutförts.

   ![Azure Database för MariaDB - Starta om lyckad](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Skapa Azure Database för MariaDB-server med Azure-portalen](./quickstart-create-mariadb-server-database-using-azure-portal.md)