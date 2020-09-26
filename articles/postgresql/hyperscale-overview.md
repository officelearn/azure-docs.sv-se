---
title: Översikt över Azure Database for PostgreSQL-storskalig (citus)
description: Ger en översikt över distributions alternativet för storskaliga (citus)
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 90d3cb106fa93649f7d6dda5ab5755061d118f66
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268391"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Vad är Azure Database for PostgreSQL-Scale (citus)?

Azure Database for PostgreSQL är en Relations databas tjänst i Microsoft-molnet som skapats för utvecklare. Den är baserad på community-versionen av [postgresql](https://www.postgresql.org/) Database Engine med öppen källkod.

Citus (storskalig) är ett distributions alternativ som horisontellt skalar frågor över flera datorer med hjälp av horisontell partitionering. Dess frågemotor parallelizes inkommande SQL-frågor över dessa servrar för snabbare svar på stora data mängder. Den hanterar program som kräver större skalning och prestanda än andra distributions alternativ: vanligt vis arbets belastningar som närmar sig – eller som redan är större än 100 GB data.

Citus (storskalig) ger:

- Horisontell skalning mellan flera datorer med horisontell partitionering
- Fråga parallellisering över dessa servrar för snabbare svar på stora data mängder
- Utmärkt stöd för program med flera klienter, operativa analyser i real tid och transaktions arbets belastningar med höga data flöden

Program som skapats för PostgreSQL kan köra distribuerade frågor på citus (storskalig) med standard [anslutnings bibliotek](./concepts-connection-libraries.md) och minimala ändringar.

## <a name="next-steps"></a>Nästa steg

- Kom igång genom [att skapa din första](./quickstart-create-hyperscale-portal.md) citus-Server grupp (Azure Database for PostgreSQL-Scale Scale).
- Se [pris sidan](https://azure.microsoft.com/pricing/details/postgresql/) för kostnads jämförelser och räknare. Citus (Scale Restore Restore Restore Restore) finns även i avsnittet om pris sidor för [storskalig (citus)](concepts-hyperscale-reserved-pricing.md) för mer information.
- Fastställ bästa [initial storlek](howto-hyperscale-scaling.md#picking-initial-size) för Server gruppen
