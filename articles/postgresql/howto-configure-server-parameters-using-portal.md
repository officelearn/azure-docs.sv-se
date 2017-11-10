---
title: "Konfigurera parametrar för server i Azure-databas för PostgreSQL via Azure portal | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar parametrar för server i Azure-databas för PostgreSQL via Azure portal."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 9e8262fbfcde2e69a656e356a7ab241f2d5043ad
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="configure-server-parameters-in-azure-portal"></a>Konfigurera serverparametrar för i Azure-portalen
Du kan visa, visa och uppdatera konfigurationsparametrar för en Azure-databas för PostgreSQL-servern via Azure-portalen.

## <a name="prerequisites"></a>Krav
Om du vill gå igenom den här instruktioner som du behöver du:
- [Azure-databas för PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visa och redigera parametrar
1. Öppna [Azure-portalen](https://portal.azure.com).

2. Välj din Azure-databas för PostgreSQL-servern.

3. Under den **inställningar** väljer **serverparametrar**. Sidan visar en lista över parametrar, värden och beskrivningar.
![Översiktssidan för parametrar](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Välj den **listrutan** för att se de möjliga värdena för uppräknade typparametrar som client_min_messages.
![Räkna upp Släpp ned](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Välj eller hovra över den **jag** (information) för att se de möjliga värdena för numeriska parametrar som cpu_index_tuple_cost.
![informationsknappen](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Om det behövs kan du använda den **sökrutan** att begränsa för en viss parameter. Sökningen är på namn och beskrivning av parametrarna.
![Sökresultat](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Ändra de parametervärden som du vill justera. Alla ändringar du gör i en session är markerade i lila. När du har ändrat värdena, kan du välja **spara**. Du kan också **Ignorera** ändringarna.
![Spara eller ignorera ändringar](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Om du har sparat nya värden för parametrarna, kan du alltid återgå allt tillbaka till standardvärdena genom att välja **Återställ till standard**.
![Återställ till standard](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Nästa steg
Lär dig mer om:
- [Översikt över serverparametrar i Azure-databas för PostgreSQL](concepts-servers.md)
- [Konfigurera parametrar med Azure CLI](howto-configure-server-parameters-using-cli.md)
