---
title: Starta om server - Azure-portal - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du kan starta om en Azure-databas för PostgreSQL - Single Server med Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770092"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Starta om Azure Database för PostgreSQL – Single Server med Azure-portalen
I det här avsnittet beskrivs hur du kan starta om en Azure-databas för PostgreSQL-server. Du kan behöva starta om servern av underhållsskäl, vilket medför ett kort avbrott när servern utför åtgärden.

Omstarten av servern blockeras om tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalning av virtuella kärnor.
 
Hur länge som krävs för att slutföra en omstart beror på PostgreSQL-återställningsprocessen. Om du vill minska omstartstiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern före omstarten.

## <a name="prerequisites"></a>Krav
För att slutföra den här guiden behöver du:
- En [Azure-databas för PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Starta om servern

Följande steg startar om PostgreSQL-servern:

1. I [Azure-portalen](https://portal.azure.com/)väljer du din Azure-databas för PostgreSQL-server.

2. Klicka på **Starta om**i verktygsfältet på serverns **översiktssida** .

   ![Azure-databas för PostgreSQL - Översikt - knappen Starta om](./media/howto-restart-server-portal/2-server.png)

3. Klicka på **Ja** om du vill bekräfta att servern startas om.

   ![Azure-databas för PostgreSQL - Starta om bekräfta](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observera att serverstatus ändras till "Starta om".

   ![Azure-databas för PostgreSQL - Omstartsstatus](./media/howto-restart-server-portal/4-restarting-status.png)

5. Bekräfta att omstarten av servern har slutförts.

   ![Azure Database för PostgreSQL - Starta om lyckad](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du [anger parametrar i Azure Database för PostgreSQL](howto-configure-server-parameters-using-portal.md)