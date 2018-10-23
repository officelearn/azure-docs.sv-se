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
ms.openlocfilehash: 696ba68894b4ac5f73c234ea49883fd0aa3db4d5
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320925"
---
# <a name="what-is-azure-database-for-mariadb"></a>Vad är Azure Database for MariaDB?

Azure Database for MariaDB är en relationsdatabastjänst i Microsofts moln. Azure Database for MariaDB baseras på databasmotorn för [MariaDB Community Edition](https://mariadb.org/download/). Den här tjänsten finns i allmän förhandsversion. 

Azure Database for MariaDB ger:

- Inbyggd hög tillgänglighet utan extra kostnad.
- Förutsägbar prestanda med hjälp av priser för användningsbaserad betalning.
- Skalning efter behov inom några sekunder.
- Säkert skydd av känsliga data som är vilande eller aktiva.
- Automatisk säkerhetskopiering och återställning av tidpunkt för upp till 35 dagar.
- Säkerhet och efterlevnad i företagsklass.

Dessa funktioner kräver nästan ingen administration. De tillhandahålls utan extra kostnad. Azure Database for MariaDB kan hjälpa dig att snabbt utveckla din app och förkorta tiden till marknaden. Du behöver inte allokera dyrbar tid och viktiga resurser för att hantera virtuella datorer och infrastruktur. Du kan även fortsätta att utveckla programmet med hjälp av de verktyg med öppen källkod och den plattform du väljer. Leverera med den hastighet och effektivitet som verksamheten kräver utan att du behöver lära dig nya färdigheter.

Mer information om de grundläggande begreppen och funktionerna i Azure Database for MariaDB, inklusive prestanda, skalbarhet och hanterbarhet, finns i de här snabbstarterna:

- [Skapa en Azure Database for MariaDB-server med hjälp av Azure-portalen](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Skapa en Azure Database for MariaDB-server med hjälp av Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder

I förhandsversionen erbjuder tjänsten Azure Database for MariaDB flera tjänstnivåer: Basic, Generell användning och Minnesoptimerad. Varje nivå erbjuder olika prestanda och funktioner för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet hjälper databasen att reagera transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i [Prisnivåer](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Hur avgör du när du ska skapa upp eller ned? Du kan använda de inbyggda funktionerna för prestandaövervakning och avisering i Azure Database for MariaDB, tillsammans med prestandaklassificering baserat på virtuella kärnor. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner de virtuella kärnorna baserat på dina aktuella eller beräknade prestandakrav. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet

Azures branschledande SLA med 99,99 % tillgänglighet (erbjuds inte under offentlig förhandsversion) drivs av ett globalt nätverk med Microsoft-hanterade datacenter. Nätverket hjälper din app att köras dygnet runt. Du kan dra nytta av inbyggda funktioner för säkerhet, feltolerans och dataskydd i Azure Database for MariaDB. Med Azure Database for MariaDB kan du använda point-in-time-återställning för att återställa en server till ett tidigare skede, så långt tillbaka som 35 dagar.

## <a name="secure-your-data"></a>Skydda dina data

Azure-databastjänster har en tradition av datasäkerhet som Azure Database for MariaDB upprätthåller. Azure Database for MariaDB ger funktioner som begränsar åtkomst, skyddar data i vila och under rörelse samt hjälper dig att övervaka aktivitet. Besök [Azure Säkerhetscenter](https://www.microsoft.com/en-us/trustcenter/security) för information om Azures plattformssäkerhet.

Azure Database for MariaDB-tjänsten använder lagringskryptering för vilande data. Data, inklusive säkerhetskopierade data, krypteras på disk. (Temporära filer som skapas av motorn när den kör frågor krypteras inte på disk.) Tjänsten använder chiffer med AES 256 bitar, vilket ingår i Azure Storage-kryptering. Nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras.

Tjänsten Azure Database for MariaDB är som standard konfigurerad att kräva [SSL-anslutningssäkerhet](./concepts-ssl-connection-security.md) för data i rörelse över nätverket. Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet. Du kan även välja att inaktivera SSL-kravet för anslutning till databastjänsten om klientprogrammet inte har stöd för SSL-anslutning.

## <a name="contacts"></a>Contacts

Du kan skicka eventuella frågor eller förslag som du har om att arbeta med Azure Database for MariaDB till [Azure Database for MariaDB-teamet](mailto:AskAzureDBforMariaDB@service.microsoft.com) (det är inte ett alias för teknisk support).

Du kan även använda följande kontaktpunkter:
- För att kontakta Azure-supporten [öppnar du en supportbegäran](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) i Azure-portalen.
- Om du vill åtgärda ett problem med ditt konto [öppnar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner skapar du ett inlägg på [Azure-feedbackforumen](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Nästa steg

Nu när du har läst en introduktion till Azure Database for MariaDB är du redo att:
- Se [prissättningssidan](https://azure.microsoft.com/pricing/details/mariadb/) för kostnadsjämförelser och kostnadsberäknare. 
- Komma igång genom att [skapa din första server](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
