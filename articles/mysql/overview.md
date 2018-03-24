---
title: Översikt över Azure-databas för MySQL relationsdatabstjänst
description: Översikt över Azure-databas för MySQL relationsdatabstjänst.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.custom: mvc
ms.openlocfilehash: 2689befc6a686d0ffc25d1fccbacb66cc6e10725
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-database-for-mysql"></a>Vad är Azure-databas för MySQL?
Azure MySQL-databas är en relationsdatabastjänst i molnet Microsoft baserat på de [MySQL Community Edition](https://www.mysql.com/products/community/) databasmotorn. Det ger Azure MySQL-databas:

- Inbyggd hög tillgänglighet utan extra kostnad.
- Förutsägbar prestanda med hjälp av priser för användningsbaserad betalning.
- Skala direkt inom några sekunder.
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
Azures branschledande 99,99% tillgänglighet servicenivåavtal (SLA) tillhandahålls av ett globalt nätverk av Microsoft-hanterade Datacenter gör att din app igång 24/7. Med varje Azure-databas för MySQL-server kan du dra nytta av inbyggd säkerhet, feltolerans och dataskydd som du annars skulle behöva köpa eller utforma, skapa och hantera. Du kan använda point-in-time-återställning med Azure-databas för MySQL för att återställa en server till ett tidigare tillstånd, så långt tillbaka som 35 dagar.

## <a name="secure-your-data"></a>Skydda dina data
Tjänster för Azure-databas har en tradition av säkerhet för data som Azure-databas för MySQL upprätthåller, med funktioner som begränsar åtkomst, skydda data i vila och under rörelse och hjälper dig att övervaka aktiviteten. Besök [Azure Säkerhetscenter](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx) för information om Azures plattformssäkerhet.

Azure-databas för MySQL-tjänst som använder kryptering för data i vila. Data, inklusive säkerhetskopior, krypteras på disken (med undantag av tillfälliga filer som skapas av motorn när du kör frågor). Tjänsten använder chiffer med AES 256 bitar som ingår i Azures lagringskryptering, och nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras.

Standard Azure-databasen för MySQL-tjänst som är konfigurerad för att kräva [SSL anslutningssäkerhet](./concepts-ssl-connection-security.md) för data i rörelse över nätverket. Att framtvinga SSL-anslutningar mellan databasservern och ditt klientprogram hjälper för att skydda mot ”man i mitten” attacker genom att kryptera dataströmmen mellan servern och ditt program.  Du kan även välja att inaktivera SSL-kravet för anslutning till databastjänsten om klientprogrammet inte har stöd för SSL-anslutning.

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till Azure-databas för MySQL och besvarat frågan ”vad är Azure-databas för MySQL”?, är du redo att:
- Se prissättningssidan för kostnadsjämförelse och Kostnadsberäknare. [Prissättning](https://azure.microsoft.com/pricing/details/mysql/)
- Kom igång genom att skapa den första servern. [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Skapa din första app med hjälp av språket du föredrar: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [gå](./connect-go.md)
