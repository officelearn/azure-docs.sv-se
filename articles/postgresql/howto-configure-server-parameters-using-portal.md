---
title: Konfigurera Server parametrar – Azure Portal-Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs hur du konfigurerar postgres-parametrarna i Azure Database for PostgreSQL via Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a9d078fe9aab12b9044733d17a1437801d5130a4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763686"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Konfigurera Server parametrar i Azure Database for PostgreSQL-enskild server via Azure Portal 
Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure Database for PostgreSQL-server via Azure Portal.

## <a name="prerequisites"></a>Krav
För att gå igenom den här instruktions guiden behöver du:
- [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visa och redigera parametrar
1. Öppna [Azure-portalen](https://portal.azure.com).

2. Välj din Azure Database for PostgreSQL-server.

3. Under avsnittet **Inställningar** väljer du **Server parametrar**. Sidan visar en lista över parametrar, deras värden och beskrivningar.
![översikts sida för parametrar](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Välj den **nedrullningsbara** knappen för att se möjliga värden för parametrar med uppräknad typ som client_min_messages.
![räkna upp List rutan](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Välj eller hovra över knappen **i** (information) för att se möjliga värden för numeriska parametrar som cpu_index_tuple_cost.
knappen ![information](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Om det behövs kan du använda **sökrutan** för att begränsa till en specifik parameter. Sökningen har ett namn och en beskrivning av parametrarna.
![Sök Resultat](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Ändra de parameter värden som du vill justera. Alla ändringar du gör i en session markeras i lila. När du har ändrat värdena kan du välja **Spara**. Eller så kan du **Ignorera** dina ändringar.
![Spara eller ta bort ändringar](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Om du har sparat nya värden för parametrarna kan du alltid återställa allt tillbaka till standardvärdena genom att välja **Återställ alla till standard**.
![Återställ alla till standard](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Nästa steg
Lär dig mer om:
- [Översikt över Server parametrar i Azure Database for PostgreSQL](concepts-servers.md)
- [Konfigurera parametrar med Azure CLI](howto-configure-server-parameters-using-cli.md)
