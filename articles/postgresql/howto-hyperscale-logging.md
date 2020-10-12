---
title: Loggar – storskalig (citus) – Azure Database for PostgreSQL
description: Så här kommer du åt databas loggar för Azure Database for PostgreSQL-storskalig (citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 1dc7bc8e119de7c8fdcf09713286be2633457486
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90895868"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Loggar i Azure Database for PostgreSQL-storskalig (citus)

PostgreSQL-loggar finns på varje nod i en citus-servergrupp (). Du kan leverera loggar till en lagrings Server eller till en Analytics-tjänst. Loggarna kan användas för att identifiera, felsöka och reparera konfigurations fel och underoptimala prestanda.

## <a name="accessing-logs"></a>Komma åt loggar

Om du vill komma åt PostgreSQL-loggar för en citus-koordinator eller arbetsnod öppnar du noden i Azure Portal:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="lista över noder":::

Öppna **diagnostikinställningar**på den markerade noden och klicka på **+ Lägg till diagnostisk inställning**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="lista över noder":::

Välj ett namn för de nya diagnostikinställningar och markera rutan **PostgreSQLLogs** .  Välj vilka mål som ska ta emot loggarna.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="lista över noder":::

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Log Analytics-frågor](/azure/azure-monitor/log-query/get-started-portal)
- Lär dig mer om [Azure Event Hub](/azure/event-hubs/event-hubs-about)
