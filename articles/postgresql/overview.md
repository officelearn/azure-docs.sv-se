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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "74481665"
---
# <a name="what-is-azure-database-for-postgresql"></a>Vad är Azure Database for PostgreSQL?
Azure Database for PostgreSQL är en Relations databas tjänst i Microsoft-molnet som skapats för utvecklare. Den är baserad på community-versionen av [postgresql](https://www.postgresql.org/) Database Engine med öppen källkod och är tillgänglig i två distributions alternativ: Single Server och Scale (citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – enskild server
Distributions alternativet för enskild server ger:

- Inbyggd [hög tillgänglighet](concepts-high-availability.md) utan extra kostnad (99,99% SLA)
- Förutsägbara prestanda med hjälp av priser enligt principen Betala per användning
- [Lodrät skala vid behov](concepts-pricing-tiers.md) inom några sekunder
- [Övervakning och aviseringar](concepts-monitoring.md) för att utvärdera din server
- Säkerhet och efterlevnad i företagsklass
- [Skyddad för att skydda](concepts-security.md) känsliga data i vila och i rörelse
- [Automatisk säkerhets kopiering och återställning](concepts-business-continuity.md) av tidpunkt i upp till 35 dagar


Alla dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. De gör att du kan fokusera på snabb program utveckling och påskynda din tid till marknaden, i stället för att ägna värdefull tid och resurser för att hantera virtuella datorer och infrastruktur. Du kan fortsätta att utveckla ditt program med de verktyg med öppen källkod och plattform som du väljer utan att behöva lära dig nya kunskaper.

Distributions alternativet för enskild server har tre pris nivåer: Basic, Generell användning och Minnesoptimerade. Varje nivå erbjuder olika resursfunktioner som har stöd för arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i  [Prisnivåer](concepts-pricing-tiers.md).

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Scale (citus)
Alternativet för skalning (citus) skalar horisontellt frågor över flera datorer med hjälp av horisontell partitionering. Dess frågemotor parallelizes inkommande SQL-frågor över dessa servrar för snabbare svar på stora data mängder. Den hanterar program som kräver större skalning och prestanda, vanligt vis arbets belastningar som närmar sig – eller som redan är större än 100 GB data.

Citus-distributions alternativet ger:

- Horisontell skalning mellan flera datorer med horisontell partitionering
- Fråga parallellisering över dessa servrar för snabbare svar på stora data mängder
- Utmärkt stöd för program med flera klienter, operativa analyser i real tid och transaktions arbets belastningar med höga data flöden

Program som skapats för PostgreSQL kan köra distribuerade frågor på citus (storskalig) med standard [anslutnings bibliotek](./concepts-connection-libraries.md) och minimala ändringar.

## <a name="contacts"></a>Contacts
Om du har frågor eller förslag om hur du arbetar med Azure Database for PostgreSQL skickar du ett e-postmeddelande till Azure Database for PostgreSQL-teamet ([ @Ask Azure dB för postgresql](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Den här adressen är för allmänna frågor snarare än support biljetter.

Tänk också på dessa kontakt punkter efter behov:
- Kontakta Azure-supporten eller åtgärda ett problem med ditt konto genom att skicka [en biljett från Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Nästa steg
- Se [pris sidan](https://azure.microsoft.com/pricing/details/postgresql/) för kostnads jämförelser och räknare.
- Kom igång genom att skapa din första Azure Database for PostgreSQL [enskild server](./quickstart-create-server-database-portal.md) eller [storskalig (citus)](./quickstart-create-hyperscale-portal.md)
- Skapa den första appen i Python, PHP, Ruby, C\#, Java, Node.js: [anslutningsbibliotek](./concepts-connection-libraries.md)
