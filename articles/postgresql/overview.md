---
title: "Översikt över relationsdatabastjänsten i Azure Database for PostgreSQL"
description: "Innehåller en översikt för relationsdatabastjänsten i Azure Database for PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 02/28/2018
ms.openlocfilehash: ef7f710fb39c3745ff5a20a73cd4dc5c1fa1fece
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="what-is-azure-database-for-postgresql"></a>Vad är Azure Database for PostgreSQL?

Azure Database for PostgreSQL är en relationsdatabastjänst i Microsoft-molnet som har utformats för utvecklare och baseras på [PostgreSQL](https://www.postgresql.org/)-databasmotorn med communityversionen av öppen källkod. Den här tjänsten finns i allmän förhandsversion. Azure Database for PostgreSQL ger:

- Inbyggd hög tillgänglighet utan extra kostnad
- Förutsägbara prestanda med hjälp av priser enligt principen Betala per användning
- Skala direkt inom några sekunder
- Säkert skydd av känsliga data som är vilande eller aktiva
- Automatisk säkerhetskopiering och återställning av tidpunkter i upp till 35 dagar
- Säkerhet och efterlevnad i företagsklass

Alla dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. Dessa funktioner gör att du kan fokusera på snabb programutveckling och att accelerera tiden till marknad, istället för att ägna värdefull tid och resurser åt att hantera virtuella datorer och infrastruktur. Du kan dessutom fortsätta att utveckla programmet med verktygen med öppen källkod och på valfri plattform, och leverera med den hastighet och effektivitet som verksamheten kräver utan att du behöver lära dig nya färdigheter. 

Den här artikeln ger en introduktion till nyckelkoncept och funktioner i Azure Database for PostgreSQL som relaterar till prestanda, skalbarhet och hanterbarhet. Läs följande snabbstarter innan du börjar:

- [Skapa en Azure Database for PostgreSQL med hjälp av Azure-portalen](quickstart-create-server-database-portal.md)
- [Skapa en Azure Database for PostgreSQL med hjälp av Azure-CLI:n](quickstart-create-server-database-azure-cli.md)

En uppsättning Azure CLI-exempel finns här:

- [Azure CLI-exempel för Azure Database for PostgreSQL](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder
Tjänsten Azure Database for PostgreSQL erbjuder tre tjänstnivåer: Basic, Generell användning och Minnesoptimerad. Varje nivå erbjuder olika resursfunktioner som har stöd för arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i [Prisnivåer](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Hur avgör du när du ska reglera upp eller ner? Använd de inbyggda övervaknings- och aviseringsfunktionerna i Azure. Med de här verktygen kan du snabbt utvärdera effekten av att skala upp eller ner baserat på dina aktuella eller beräknade prestanda- eller lagringsbehov. Mer information finns i [Aviseringar](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande serviceavtal (SLA) med 99,99 % tillgänglighet (finns ej i förhandsversion), drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång dygnet runt. Med varje Azure Database for PostgreSQL-server drar du nytta av inbyggd säkerhet, feltolerans och dataskydd som du annars skulle behöva köpa, utforma, utveckla och hantera. Med Azure Database for PostgreSQL erbjuder varje prisnivå en omfattande uppsättning funktioner för affärskontinuitet och alternativ som du kan använda för att komma igång. Du kan använda [point-in-time-återställning](howto-restore-server-portal.md) för att återställa en databas till ett tidigare skede, så långt tillbaka som 35 dagar. Om det uppstår ett avbrott i datacentret som är värd för dina databasmiljöer kan du återställa databaser från geo-redundanta kopior av säkerhetskopior som nyligen skapats.

## <a name="secure-your-data"></a>Skydda dina data
Azure-databastjänster har en tradition av datasäkerhet som Azure Database for PostgreSQL upprätthåller med funktioner som begränsar åtkomst, skyddar data i vila och under rörelse och hjälper dig att övervaka aktivitet. Besök [Azure Säkerhetscenter](https://www.microsoft.com/TrustCenter/Security/default.aspx) för information om Azures plattformssäkerhet.

Tjänsten Azure Database for PostgreSQL använder lagringskryptering för data i vila. Data, inklusive säkerhetskopior, krypteras på disk (med undantag för tillfälliga filer som skapas av motorn vid körning av frågor). Tjänsten använder chiffer med AES 256 bitar som ingår i Azures lagringskryptering, och nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras.

Tjänsten Azure Database for PostgreSQL är som standard konfigurerad för att kräva [SSL-anslutningssäkerhet](./concepts-ssl-connection-security.md) för data i rörelse över nätverket. Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.  Du kan även välja att inaktivera SSL-kravet för anslutning till databastjänsten om klientprogrammet inte har stöd för SSL-anslutning.

## <a name="next-steps"></a>Nästa steg
- På [prissättningssidan](https://azure.microsoft.com/pricing/details/postgresql/) finns kostnadsjämförelser och kostnadsberäknare.
- Kom igång genom att [skapa din första Azure Database for PostgreSQL](./quickstart-create-server-database-portal.md).
- Skapa den första appen i Python, PHP, Ruby, C\#, Java, Node.js: [anslutningsbibliotek](./concepts-connection-libraries.md)
