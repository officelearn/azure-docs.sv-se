---
title: "Översikt för relationsdatabastjänst i Azure Database for PostgreSQL | Microsoft Docs"
description: "Innehåller en översikt för relationsdatabastjänsten i Azure Database for PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 10/20/2017
ms.openlocfilehash: 9aa24dd10ef29c716c05cafeb84e0beb23d50628
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="what-is-azure-database-for-postgresql"></a>Vad är Azure Database for PostgreSQL?

Azure Database for PostgreSQL är en relationsdatabastjänst i Microsoft-molnet som är utformad för utvecklare baserat på [PostgreSQL](https://www.postgresql.org/)-databasmotorn med communityversionen av öppen källkod. Den här tjänsten finns i allmän förhandsversion. Azure Database for PostgreSQL ger:

- Inbyggd hög tillgänglighet utan extra kostnad.
- Förutsägbar prestanda med hjälp av priser för användningsbaserad betalning.
- Skala direkt inom några sekunder.
- Säkrad för att skydda känsliga data i vila och under rörelse.
- Automatisk säkerhetskopiering och återställning av tidpunkt för upp till 35 dagar.
- Säkerhet och efterlevnad i företagsklass.

Alla dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. Dessa funktioner gör att du kan fokusera på snabb programutveckling och att accelerera tiden till marknad, istället för att ägna värdefull tid och resurser åt att hantera virtuella datorer och infrastruktur. Du kan dessutom fortsätta att utveckla programmet med verktygen med öppen källkod och valfri plattform, och leverera med den hastighet och effektivitet som verksamheten kräver utan att du måste lära dig nya förmågor. 

Den här artikeln ger en introduktion till nyckelkoncept och funktioner i Azure Database for PostgreSQL som relaterar till prestanda, skalbarhet och hanterbarhet. Läs följande snabbstarter innan du börjar:

- [Skapa en Azure Database for PostgreSQL med hjälp av Azure-portalen](quickstart-create-server-database-portal.md)
- [Skapa en Azure Database for PostgreSQL med hjälp av Azure-CLI:n](quickstart-create-server-database-azure-cli.md)

En uppsättning Azure CLI-exempel finns här:

- [Azure CLI-exempel för Azure Database for PostgreSQL](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder
I förhandsversionen har tjänsten Azure Database for PostgreSQL två tjänstnivåer: Basic och Standard. Varje nivå erbjuder olika prestanda och funktioner för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i [Prisnivåer](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Hur avgör du när du ska reglera upp eller ner? Du kan använda de inbyggda funktionerna för prestandaövervakning och avisering, tillsammans med prestandaklassificering baserat på beräkningsenheter. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner beräkningsenheterna baserat på dina aktuella eller beräknade prestandakrav. Mer information finns i [Aviseringar](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande serviceavtal (SLA) med 99,99 % tillgänglighet (finns ej i förhandsversion), drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång dygnet runt. Med varje Azure Database for PostgreSQL-server drar du nytta av inbyggd säkerhet, feltolerans och dataskydd som du annars skulle behöva köpa, utforma, utveckla och hantera. Med Azure Database for PostgreSQL erbjuder varje tjänstnivå en omfattande uppsättning funktioner för affärskontinuitet och alternativ som du kan använda för att komma igång. Du kan använda [point-in-time-återställning](howto-restore-server-portal.md) för att återställa en databas till ett tidigare skede, så långt tillbaka som 35 dagar. Om det uppstår ett avbrott i datacentret som är värd för dina databasmiljöer kan du återställa databaser från geo-redundanta kopior av säkerhetskopior som nyligen skapats.

## <a name="secure-your-data"></a>Skydda dina data
Azure-databastjänster har en tradition av datasäkerhet som Azure Database for PostgreSQL upprätthåller med funktioner som begränsar åtkomst, skyddar data i vila och under rörelse och hjälper dig att övervaka aktivitet. Besök [Azure Säkerhetscenter](https://www.microsoft.com/TrustCenter/Security/default.aspx) för information om Azures plattformssäkerhet.

Tjänsten Azure Database for PostgreSQL använder lagringskryptering för data i vila. Data, inklusive säkerhetskopior, krypteras på disk (med undantag för tillfälliga filer som skapas av motorn vid körning av frågor). Tjänsten använder chiffer med AES 256 bitar som ingår i Azures lagringskryptering, och nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras.

Tjänsten Azure Database for PostgreSQL är som standard konfigurerad för att kräva [SSL-anslutningssäkerhet](./concepts-ssl-connection-security.md) för data i rörelse över nätverket. Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.  Du kan även välja att inaktivera SSL-kravet för anslutning till databastjänsten om klientprogrammet inte har stöd för SSL-anslutning.

## <a name="next-steps"></a>Nästa steg
- På [prissättningssidan](https://azure.microsoft.com/pricing/details/postgresql/) finns kostnadsjämförelser och kostnadsberäknare.
- Kom igång genom att [skapa din första Azure Database for PostgreSQL](./quickstart-create-server-database-portal.md).
- Skapa den första appen i Python, PHP, Ruby, C\#, Java, Node.js: [anslutningsbibliotek](./concepts-connection-libraries.md)
