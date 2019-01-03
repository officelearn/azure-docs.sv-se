---
title: Konfigurera serverparametrar i Azure Database för PostgreSQL via Azure portal
description: Den här artikeln beskriver hur du konfigurerar serverparametrarna i Azure Database för PostgreSQL via Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0d0626c48ecebdead604aab93ab0602c698d0d77
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540545"
---
# <a name="configure-server-parameters-in-azure-portal"></a>Konfigurera serverparametrar i Azure portal
Du kan lista, visa och uppdatera konfigurationsparametrar för en Azure Database for PostgreSQL-server via Azure portal.

## <a name="prerequisites"></a>Förutsättningar
Att gå igenom den här guiden behöver du:
- [Azure Database for PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visa och redigera parametrar
1. Öppna [Azure-portalen](https://portal.azure.com).

2. Välj din Azure Database for PostgreSQL-server.

3. Under den **inställningar** väljer **serverparametrar**. Sidan visar en lista över parametrar, värden och beskrivningar.
![Översiktssidan för parametrar](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Välj den **nedrullningsbar listruta** för att se de möjliga värdena för uppräknade typparametrar som client_min_messages.
![Räkna upp Släpp ned](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Välj eller hovra över den **jag** (information) för att se vilka möjliga värden för numeriska parametrar som cpu_index_tuple_cost.
![informationsknappen](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Om du behöver den **sökrutan** att begränsa till en specifik parameter. Sökningen är på namn och beskrivning av parametrarna.
![Sökresultat](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Ändra de parametervärden som du vill justera. Alla ändringar du gör i en session är markerade i lila. När du har ändrat värdena kan du välja **spara**. Eller så kan du **Ignorera** dina ändringar.
![Spara eller ignorera ändringar](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Om du har sparat nya värden för parametrarna, kan du alltid återgå allt till standardvärden genom att välja **Återställ allt till standardinställningarna**.
![Återställ allt till standardinställningarna](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om:
- [Översikt av Azure Database för PostgreSQL-serverparametrar](concepts-servers.md)
- [Konfigurera parametrar med Azure CLI](howto-configure-server-parameters-using-cli.md)
