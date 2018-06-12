---
title: Översikt över Azure-databas för MySQL relationsdatabstjänst
description: Översikt över Azure-databas för MySQL relationsdatabstjänst.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/02/2018
ms.custom: mvc
ms.openlocfilehash: b7af709c4175ecd6100de6d638ac9862488a7190
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266354"
---
# <a name="what-is-azure-database-for-mysql"></a>Vad är Azure-databas för MySQL?
Azure MySQL-databas är en relationsdatabastjänst i molnet Microsoft baserat på de [MySQL Community Edition](https://www.mysql.com/products/community/) databasmotorn. Det ger Azure MySQL-databas:

- Inbyggd hög tillgänglighet utan extra kostnad.
- Förutsägbar prestanda med hjälp av priser för användningsbaserad betalning.
- Skala efter behov inom några sekunder.
- Säkrad för att skydda känsliga data i vila och under rörelse.
- Automatisk säkerhetskopiering och återställning av tidpunkt för upp till 35 dagar.
- Säkerhet och efterlevnad i företagsklass.

Dessa funktioner kräver nästan inga administration och alla tillhandahålls utan extra kostnad. De kan du fokusera på snabb apputveckling och accelerera din tid till marknad snarare än allokera värdefull tid och resurser för att hantera virtuella datorer och infrastruktur. Du kan dessutom fortsätta att utveckla ditt program med öppen källkod verktyg och plattform som du väljer att ge med snabbare och mer effektiv dina affärsbehov, allt utan att behöva veta nya kunskaper.

![Azure-databas för MySQL konceptuellt diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Den här artikeln ger en introduktion till Azure-databas för MySQL nyckelkoncept och funktioner som är relaterade till prestanda, skalbarhet och hanterbarhet, med länkar till mer detaljerad information. Läs följande snabbstarter innan du börjar:
- [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

En uppsättning Azure CLI-exempel finns här:
- [Azure CLI-exempel för Azure-databas för MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder
Azure-databas för MySQL-tjänst som erbjuder flera servicenivåer: Basic generella och Minnesoptimerade. Varje nivå erbjuder olika prestanda och funktioner för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i [Prisnivåer](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Hur avgör du när du ska reglera upp eller ner? Du kan använda inbyggda prestanda övervakning och avisering funktioner, kombinerat med klassificeringarna prestanda baserat på vCores. Använda dessa verktyg kan du snabbt utvärdera effekten av skalning vCores uppåt eller nedåt baserat på din aktuella eller planerade prestandabehov. Mer information finns i [Aviseringar](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande serviceavtal (SLA) med 99,99 % tillgänglighet, drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång 24/7. Med varje Azure-databas för MySQL-server kan du dra nytta av inbyggd säkerhet, feltolerans och dataskydd som du annars skulle behöva köpa eller utforma, skapa och hantera. Du kan använda point-in-time-återställning med Azure-databas för MySQL för att återställa en server till ett tidigare tillstånd, så långt tillbaka som 35 dagar.

## <a name="secure-your-data"></a>Skydda dina data
Tjänster för Azure-databas har en tradition av säkerhet för data som Azure-databas för MySQL upprätthåller, med funktioner som begränsar åtkomst, skydda data i vila och under rörelse och hjälper dig att övervaka aktiviteten. Besök [Azure Säkerhetscenter](https://www.microsoft.com/en-us/trustcenter/security) för information om Azures plattformssäkerhet.

Azure-databas för MySQL-tjänst som använder kryptering för data i vila. Data, inklusive säkerhetskopior, krypteras på disken (med undantag av tillfälliga filer som skapas av motorn när du kör frågor). Tjänsten använder chiffer med AES 256 bitar som ingår i Azures lagringskryptering, och nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras.

Standard Azure-databasen för MySQL-tjänst som är konfigurerad för att kräva [SSL anslutningssäkerhet](./concepts-ssl-connection-security.md) för data i rörelse över nätverket. Att framtvinga SSL-anslutningar mellan databasservern och ditt klientprogram hjälper för att skydda mot ”man i mitten” attacker genom att kryptera dataströmmen mellan servern och ditt program. Du kan även välja att inaktivera SSL-kravet för anslutning till databastjänsten om klientprogrammet inte har stöd för SSL-anslutning.

## <a name="contacts"></a>Contacts
För några frågor eller förslag som du kanske har om att arbeta med Azure-databas för MySQL, skicka ett e-postmeddelande till Azure-databasen för MySQL-teamet ([ @Ask Azure DB för MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Observera att detta inte är ett alias för teknisk support.

Dessutom, Tänk på följande kontaktpunkten efter behov:
- Kontakta Azure Support [filen en biljett från Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, filen en [supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Ge feedback eller efterfråga nya funktioner, skapa en post via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till Azure-databas för MySQL och besvarat frågan ”vad är Azure-databas för MySQL”?, är du redo att:
- Se prissättningssidan för kostnadsjämförelse och Kostnadsberäknare. [Prissättning](https://azure.microsoft.com/pricing/details/mysql/)
- Kom igång genom att skapa den första servern. [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Skapa din första app med hjälp av språket du föredrar: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [gå](./connect-go.md)
