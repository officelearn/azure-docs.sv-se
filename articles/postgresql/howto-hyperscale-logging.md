---
title: Loggar – storskalig (citus) – Azure Database for PostgreSQL
description: Så här kommer du åt databas loggar för Azure Database for PostgreSQL-storskalig (citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100216"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Loggar i Azure Database for PostgreSQL-storskalig (citus)

PostgreSQL-loggar finns på varje nod i en citus-servergrupp (). Du kan leverera loggar till en lagrings Server eller till en Analytics-tjänst. Loggarna kan användas för att identifiera, felsöka och reparera konfigurations fel och underoptimala prestanda.

## <a name="accessing-logs"></a>Komma åt loggar

Om du vill komma åt PostgreSQL-loggar för en citus-koordinator eller arbetsnod öppnar du noden i Azure Portal:

![lista över noder](media/howto-hyperscale-logging/choose-node.png)

Öppna **diagnostikinställningar**på den markerade noden och klicka på **+ Lägg till diagnostisk inställning**.

![Knappen Lägg till diagnostikinställningar](media/howto-hyperscale-logging/diagnostic-settings.png)

Välj ett namn för de nya diagnostikinställningar och markera rutan **PostgreSQLLogs** .  Välj vilka mål som ska ta emot loggarna.

![Välj PostgreSQL-loggar](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Log Analytics-frågor](/azure/azure-monitor/log-query/get-started-portal)
- Lär dig mer om [Azure Event Hub](/azure/event-hubs/event-hubs-about)
