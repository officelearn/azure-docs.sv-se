---
title: Konfigurera serverparametrar - Azure-portal - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du konfigurerar Postgres-parametrarna i Azure Database för PostgreSQL via Azure-portalen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a9d078fe9aab12b9044733d17a1437801d5130a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763686"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Konfigurera serverparametrar i Azure Database för PostgreSQL - Single Server via Azure-portalen 
Du kan lista, visa och uppdatera konfigurationsparametrar för en Azure-databas för PostgreSQL-server via Azure-portalen.

## <a name="prerequisites"></a>Krav
För att gå igenom denna hur-till guide du behöver:
- [Azure-databas för PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visa och redigera parametrar
1. Öppna [Azure-portalen](https://portal.azure.com).

2. Välj din Azure Database for PostgreSQL-server.

3. Välj **Serverparametrar**under avsnittet **INSTÄLLNINGAR** . Sidan visar en lista över parametrar, deras värden och beskrivningar.
![Översiktssida för parametrar](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Välj **nedrullningsbara** knappen om du vill se möjliga värden för uppräknade parametrar som client_min_messages.
![Räkna upp nedsläpp](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Markera eller hovra över **i-knappen** (information) om du vill visa intervallet för möjliga värden för numeriska parametrar som cpu_index_tuple_cost.
![informationsknapp](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Om det behövs använder du **sökrutan** för att begränsa till en viss parameter. Sökningen är på namn och beskrivning av parametrarna.
![Sökresultat](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Ändra de parametervärden som du vill justera. Alla ändringar du gör i en session markeras i lila. När du har ändrat värdena kan du välja **Spara**. Eller så kan du **ignorera** ändringarna.
![Spara eller ignorera ändringar](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Om du har sparat nya värden för parametrarna kan du alltid återställa allt till standardvärdena genom att välja **Återställ alla till standard**.
![Återställa alla till standard](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Nästa steg
Läs om:
- [Översikt över serverparametrar i Azure Database för PostgreSQL](concepts-servers.md)
- [Konfigurera parametrar med hjälp av Azure CLI](howto-configure-server-parameters-using-cli.md)
