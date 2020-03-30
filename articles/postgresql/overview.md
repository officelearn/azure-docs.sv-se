---
title: Översikt över relationsdatabastjänsten i Azure Database for PostgreSQL
description: Innehåller en översikt för relationsdatabastjänsten i Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 9ea0610811f6906526afe55d577e04a8decd5f49
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74481665"
---
# <a name="what-is-azure-database-for-postgresql"></a>Vad är Azure Database for PostgreSQL?
Azure Database for PostgreSQL är en relationsdatabastjänst i Microsoft-molnet som skapats för utvecklare. Den är baserad på community-versionen av [PostgreSQL-databasmotor](https://www.postgresql.org/) med öppen källkod och finns i två distributionsalternativ: Single Server och Hyperscale (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – enskild server
Distributionsalternativet för en server ger:

- Inbyggd [hög tillgänglighet](concepts-high-availability.md) utan extra kostnad (99,99% SLA)
- Förutsägbara prestanda med hjälp av priser enligt principen Betala per användning
- [Vertikal skala efter behov](concepts-pricing-tiers.md) inom några sekunder
- [Övervakning och avisering](concepts-monitoring.md) för att bedöma din server
- Säkerhet och efterlevnad i företagsklass
- [Säkrad för att skydda](concepts-security.md) känsliga data i vila och i rörelse
- [Automatiska säkerhetskopieringar och point-in-time-restore i](concepts-business-continuity.md) upp till 35 dagar


Alla dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. De gör att du kan fokusera på snabb applikationsutveckling och påskynda din tid till marknaden, snarare än att spendera dyrbar tid och resurser för att hantera virtuella datorer och infrastruktur. Du kan fortsätta att utveckla ditt program med de verktyg och en plattform med öppen källkod, utan att behöva lära dig nya färdigheter.

Distributionsalternativet för en server erbjuder tre prisnivåer: Grundläggande, allmänt syfte och Minnesoptimerad. Varje nivå erbjuder olika resursfunktioner som har stöd för arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i  [Prisnivåer](concepts-pricing-tiers.md).

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure-databas för PostgreSQL - Hyperskala (Citus)
Alternativet Hyperskala (Citus) skalar vågrätt frågor över flera datorer med hjälp av sharding. Dess frågemotor parallelliserar inkommande SQL-frågor över dessa servrar för snabbare svar på stora datauppsättningar. Den betjänar program som kräver större skala och prestanda, i allmänhet arbetsbelastningar som närmar sig - eller redan överstiger - 100 GB data.

Distributionsalternativet Hyperscale (Citus) ger:

- Vågrät skalning över flera datorer med hjälp av fragmentering
- Frågeparallalisering på dessa servrar för snabbare svar på stora datauppsättningar
- Utmärkt stöd för program med flera innehavare, driftanalys i realtid och transaktionsarbetsbelastningar med hög dataflöde

Program som skapats för PostgreSQL kan köra distribuerade frågor på Hyperscale (Citus) med [standardanslutningsbibliotek](./concepts-connection-libraries.md) och minimala ändringar.

## <a name="contacts"></a>Contacts
Om du vill ha frågor eller förslag om hur du arbetar med Azure Database for PostgreSQL skickar du ett e-postmeddelande till Azure Database for PostgreSQL Team ([ @Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Denna adress är för allmänna frågor snarare än supportbiljetter.

Beakta dessutom dessa kontaktpunkter som lämpliga:
- Om du vill kontakta Azure Support eller åtgärda ett problem med ditt konto [lämnar du in en biljett från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Nästa steg
- Se [prissidan](https://azure.microsoft.com/pricing/details/postgresql/) för kostnadsjämställare och kalkylatorer.
- Komma igång genom att skapa din första Azure-databas för PostgreSQL [Single Server](./quickstart-create-server-database-portal.md) eller [Hyperscale (Citus)](./quickstart-create-hyperscale-portal.md)
- Skapa den första appen i Python, PHP, Ruby, C\#, Java, Node.js: [anslutningsbibliotek](./concepts-connection-libraries.md)
