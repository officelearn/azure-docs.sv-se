---
title: Översikt över relationsdatabastjänsten i Azure Database for PostgreSQL
description: Innehåller en översikt för relationsdatabastjänsten i Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: f4023fa84215a0319669de0d812d8306b62278e3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073274"
---
# <a name="what-is-azure-database-for-postgresql"></a>Vad är Azure Database for PostgreSQL?
Azure Database för PostgreSQL är en relationsdatabastjänst i Microsoft-molnet som byggts för utvecklare. Den är baserad på communityversionen av öppen källkod [PostgreSQL](https://www.postgresql.org/) databasmotor och är tillgänglig i två distributionsalternativ: Enskild Server och hyperskala (Citus) (förhandsversion).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database för PostgreSQL – enskild Server
Distributionsalternativ för enskild Server ger:

- Inbyggd hög tillgänglighet utan extra kostnad (99,99% SLA)
- Förutsägbara prestanda med hjälp av priser enligt principen Betala per användning
- Lodrät skalning vid behov inom några sekunder
- Övervakning och avisering för att snabbt utvärdera effekten av att skala
- Säkert skydd av känsliga data som är vilande eller aktiva
- Automatisk säkerhetskopiering och återställning av tidpunkter i upp till 35 dagar
- Säkerhet och efterlevnad i företagsklass

Alla dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. De gör att du kan fokusera på snabb programutveckling och accelerera din tid till marknad, istället för slösa dyrbar tid och resurser för att hantera virtuella datorer och infrastruktur. Du kan fortsätta att utveckla ditt program med öppen källkod och plattform, utan att behöva lära dig nya färdigheter.

Distributionsalternativ för enskild Server erbjuder tre tjänstnivåer: Grundläggande, generell användning och minnesoptimerad. Varje nivå erbjuder olika resursfunktioner som har stöd för arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i  [Prisnivåer](concepts-pricing-tiers.md).

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)
Alternativet hyperskala (Citus) skalas vågrätt frågor över flera datorer med horisontell partitionering. Dess frågemotorn parallelizes inkommande SQL-frågor på dessa servrar för snabbare svar på stora datauppsättningar. Den fungerar program som kräver större skala och prestanda, vanligtvis arbetsbelastningar som närmar-- eller redan överskrider – 100 GB data.

Alternativ för distribution av storskaliga (Citus) ger:

- Horisontell skalning över flera datorer som använder horisontell partitionering
- Frågeparallellisering på dessa servrar för snabbare svar på stora datauppsättningar
- Utmärkt stöd för program med flera klienter, operativ analys i realtid och stora dataflöden transaktionsbelastningar

Program som skapats för PostgreSQL kan du köra distribuerade frågor på hyperskala (Citus) med standard [anslutningsbibliotek](./concepts-connection-libraries.md) och minimala ändringar.

Observera att hyperskala (Citus) finns i offentlig förhandsversion och därmed ännu erbjuder inte något serviceavtal.

## <a name="data-security"></a>Datasäkerhet
Azure Database för PostgreSQL upprätthåller i Azure database services tradition av datasäkerhet. Det har funktioner som begränsar åtkomst, skydda data i vila och under rörelse och hjälper dig att övervaka aktiviteten. Besök [Azure Säkerhetscenter](https://azure.microsoft.com/overview/trusted-cloud/) för information om Azures plattformssäkerhet.

Azure Database for PostgreSQL-tjänsten använder lagringskryptering för vilande data och är FIPS 140-2-kompatibel. Data, inklusive säkerhetskopior, krypteras på disk. Tjänsten använder AES-256-bitars-chiffer som ingår i Azure storage-kryptering och nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras. Som standard kräver Azure Database for PostgreSQL-tjänsten säkra anslutningar för data i rörelse både över nätverket och mellan databasen och klienten programmet.

## <a name="contacts"></a>Contacts
Några frågor eller förslag om hur du arbetar med Azure Database för PostgreSQL, skicka ett e-postmeddelande till Azure Database för PostgreSQL-teamet ([ @Ask Azure DB för PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Den här adressen är för allmänna frågor i stället för supportärenden.

Dessutom tänka på följande kontaktpunkten efter behov:
- Kontakta Azure-supporten eller åtgärda ett problem med ditt konto, [lämna in en biljett från Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Nästa steg
- På [prissättningssidan](https://azure.microsoft.com/pricing/details/postgresql/) finns kostnadsjämförelser och kostnadsberäknare.
- Kom igång genom att skapa din första Azure Database för PostgreSQL [enskild Server](./quickstart-create-server-database-portal.md) eller [hyperskala (Citus) (förhandsversion)](./quickstart-create-hyperscale-portal.md)
- Skapa din första app i Python, PHP, Ruby, C\#, Java, Node.js: [Anslutningsbibliotek](./concepts-connection-libraries.md)
