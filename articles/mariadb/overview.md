---
title: Översikt över relationsdatabastjänsten i Azure Database for MariaDB
description: Översikt över relationsdatabastjänsten i Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: overview
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 3649a173d5707179ca8547a8169b7d308c4f7f1c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249170"
---
# <a name="what-is-azure-database-for-mariadb"></a>Vad är Azure Database for MariaDB?
Azure Database for MariaDB är en relationsdatabastjänst i Microsoft-molnet som är baserad på databasmotorn [MariaDB Community Edition](https://mariadb.org/download/). Den här tjänsten finns i allmän förhandsversion. Azure Database for MariaDB ger:

- Inbyggd hög tillgänglighet utan extra kostnad.
- Förutsägbar prestanda med hjälp av priser för användningsbaserad betalning.
- Skala efter behov inom några sekunder.
- Säkrad för att skydda känsliga data i vila och under rörelse.
- Automatisk säkerhetskopiering och återställning av tidpunkt för upp till 35 dagar.
- Säkerhet och efterlevnad i företagsklass.

Dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. De gör att du kan fokusera på snabb apputveckling och att accelerera din tid till marknaden, istället för att ägna värdefull tid och resurser åt att hantera virtuella datorer och infrastruktur. Du kan dessutom fortsätta att utveckla programmet med verktygen med öppen källkod och på valfri plattform, och leverera med den hastighet och effektivitet som verksamheten kräver utan att du behöver lära dig nya färdigheter.

Den här artikeln ger en introduktion till nyckelkoncept och funktioner för Azure Database for MariaDB som relaterar till prestanda, skalbarhet och hanterbarhet, med länkar till mer detaljerad information. Läs följande snabbstarter innan du börjar:
- [Skapa en Azure Database for MariaDB-server med Azure-portalen](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Skapa en Azure Database for MariaDB-server med Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder
I förhandsversionen erbjuder tjänsten Azure Database for MariaDB flera tjänstnivåer: Basic, Generell användning och Minnesoptimerad. Varje nivå erbjuder olika prestanda och funktioner för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i [Prisnivåer](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Hur avgör du när du ska reglera upp eller ner? Du kan använda de inbyggda funktionerna för prestandaövervakning och avisering, tillsammans med prestandaklassificering baserat på virtuella kärnor. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner de virtuella kärnorna baserat på dina aktuella eller beräknade prestandakrav. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande serviceavtal (SLA) med 99,99 % tillgänglighet (erbjuds inte under offentlig förhandsversion), drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång dygnet runt. Med varje Azure Database for MariaDB-server drar du nytta av inbyggd säkerhet, feltolerans och dataskydd som du annars skulle behöva köpa, utforma, utveckla och hantera. Med Azure Database for MariaDB kan du använda point-in-time-återställning för att återställa en server till ett tidigare skede, så långt tillbaka som 35 dagar.

## <a name="secure-your-data"></a>Skydda dina data
Azure-databastjänster har en tradition av datasäkerhet som Azure Database for MariaDB upprätthåller med funktioner som begränsar åtkomst, skyddar data i vila och under rörelse och hjälper dig att övervaka aktivitet. Besök [Azure Säkerhetscenter](https://www.microsoft.com/en-us/trustcenter/security) för information om Azures plattformssäkerhet.

Tjänsten Azure Database for MariaDB använder lagringskryptering för data i vila. Data, inklusive säkerhetskopior, krypteras på disk (med undantag för tillfälliga filer som skapas av motorn vid körning av frågor). Tjänsten använder chiffer med AES 256 bitar som ingår i Azures lagringskryptering, och nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras.

Tjänsten Azure Database for MariaDB är som standard konfigurerad att kräva [SSL-anslutningssäkerhet](./concepts-ssl-connection-security.md) för data i rörelse över nätverket. Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet. Du kan även välja att inaktivera SSL-kravet för anslutning till databastjänsten om klientprogrammet inte har stöd för SSL-anslutning.

## <a name="contacts"></a>Contacts
För frågor eller förslag som du kan ha om att arbeta med Azure Database for MariaDB, skickar du ett e-postmeddelande till Azure Database for MariaDB-teamet ([@Ask Azure DB för MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com)). Observera att detta inte är ett alias för teknisk support.

Tänk dessutom på följande kontaktpunkter efter behov:
- Kontakta Azure Support genom att [skicka in ett supportärende från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till SQL Database for MariaDB och besvarat frågan ”Vad är SQL Database for MariaDB?” är du redo att:
- På prissättningssidan finns kostnadsjämförelser och kostnadsberäknare. [Prissättning](https://azure.microsoft.com/pricing/details/mariadb/)
- Kom igång genom att skapa din första server. [Skapa en Azure Database for MariaDB-server med Azure-portalen](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
