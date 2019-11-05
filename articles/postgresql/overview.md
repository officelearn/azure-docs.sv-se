---
title: Översikt över relationsdatabastjänsten i Azure Database for PostgreSQL
description: Innehåller en översikt för relationsdatabastjänsten i Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 9d4389ed2c0cc5d7b632380fba691ae2e9a6b257
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466750"
---
# <a name="what-is-azure-database-for-postgresql"></a>Vad är Azure Database for PostgreSQL?
Azure Database for PostgreSQL är en Relations databas tjänst i Microsoft-molnet som skapats för utvecklare. Den är baserad på community-versionen av [postgresql](https://www.postgresql.org/) Database Engine med öppen källkod och är tillgänglig i två distributions alternativ: Single Server och Scale (citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL-enskild server
Distributions alternativet för enskild server ger:

- Inbyggd hög tillgänglighet utan extra kostnad (99,99% SLA)
- Förutsägbara prestanda med hjälp av priser enligt principen Betala per användning
- Lodrät skala vid behov inom några sekunder
- Övervakning och avisering för att snabbt utvärdera effekten av skalning
- Säkert skydd av känsliga data som är vilande eller aktiva
- Automatisk säkerhetskopiering och återställning av tidpunkter i upp till 35 dagar
- Säkerhet och efterlevnad i företagsklass

Alla dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. De gör att du kan fokusera på snabb program utveckling och påskynda din tid till marknaden, i stället för att ägna värdefull tid och resurser för att hantera virtuella datorer och infrastruktur. Du kan fortsätta att utveckla ditt program med de verktyg med öppen källkod och plattform som du väljer utan att behöva lära dig nya kunskaper.

Distributions alternativet för enskild server har tre pris nivåer: Basic, Generell användning och Minnesoptimerade. Varje nivå erbjuder olika resursfunktioner som har stöd för arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i  [Prisnivåer](concepts-pricing-tiers.md).

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Scale (citus)
Alternativet för skalning (citus) skalar horisontellt frågor över flera datorer med hjälp av horisontell partitionering. Dess frågemotor parallelizes inkommande SQL-frågor över dessa servrar för snabbare svar på stora data mängder. Den hanterar program som kräver större skalning och prestanda, vanligt vis arbets belastningar som närmar sig – eller som redan är större än 100 GB data.

Citus-distributions alternativet ger:

- Horisontell skalning mellan flera datorer med horisontell partitionering
- Fråga parallellisering över dessa servrar för snabbare svar på stora data mängder
- Utmärkt stöd för program med flera klienter, operativa analyser i real tid och transaktions arbets belastningar med höga data flöden

Program som skapats för PostgreSQL kan köra distribuerade frågor på citus (storskalig) med standard [anslutnings bibliotek](./concepts-connection-libraries.md) och minimala ändringar.

## <a name="data-security"></a>Datasäkerhet
Azure Database for PostgreSQL överlagrar Azure Database Services-tradition av data säkerhet. Den har funktioner som begränsar åtkomst, skyddar data i vila och i rörelse och hjälper dig att övervaka aktiviteten. Besök [Azure Säkerhetscenter](https://azure.microsoft.com/overview/trusted-cloud/) för information om Azures plattformssäkerhet.

Tjänsten Azure Database for PostgreSQL använder FIPS 140-2-validerade kryptografisk modul för lagrings kryptering av data i vila. Data, inklusive säkerhets kopior, krypteras på disk med undantag för tillfälliga filer som skapas vid körning av frågor. Tjänsten använder AES 256-bit-chiffer som ingår i Azure Storage-kryptering och nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras. Som standard kräver Azure Database for PostgreSQL tjänsten säkra anslutningar för data i rörelse både i nätverket och mellan databasen och klient programmet.

## <a name="contacts"></a>Contacts
Om du har frågor eller förslag om hur du arbetar med Azure Database for PostgreSQL skickar du ett e-postmeddelande till Azure Database for PostgreSQL-teamet ([@Ask Azure dB för postgresql](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Den här adressen är för allmänna frågor snarare än support biljetter.

Tänk också på dessa kontakt punkter efter behov:
- Kontakta Azure-supporten eller åtgärda ett problem med ditt konto genom att skicka [en biljett från Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Nästa steg
- På [prissättningssidan](https://azure.microsoft.com/pricing/details/postgresql/) finns kostnadsjämförelser och kostnadsberäknare.
- Kom igång genom att skapa din första Azure Database for PostgreSQL [enskild server](./quickstart-create-server-database-portal.md) eller [storskalig (citus)](./quickstart-create-hyperscale-portal.md)
- Skapa den första appen i Python, PHP, Ruby, C\#, Java, Node.js: [anslutningsbibliotek](./concepts-connection-libraries.md)
