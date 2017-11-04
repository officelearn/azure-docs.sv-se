---
title: "Översikt över Azure-databas för MySQL relationsdatabstjänst | Microsoft Docs"
description: "Översikt över Azure-databas för MySQL relationsdatabstjänst."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/20/2017
ms.custom: mvc
ms.openlocfilehash: 2a9efdd9285dfa5fca450ede64e5f6ee54cbc72b
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2017
---
# <a name="what-is-azure-database-for-mysql"></a>Vad är Azure-databas för MySQL?
Azure MySQL-databas är en relationsdatabastjänst i molnet Microsoft baserat på de [MySQL Community Edition](https://www.mysql.com/products/community/) databasmotorn. Tjänsten är tillgänglig som förhandsversion. Det ger Azure MySQL-databas:

- Inbyggd hög tillgänglighet med utan extra kostnad.
- Förutsägbar prestanda, använder inklusiva betalning per användning priser.
- Skala direkt inom några sekunder.
- Skyddad för att skydda känsliga data i vila och under rörelse.
- Automatisk säkerhetskopiering och punkt-i-tid-återställning för upp till 35 dagar.
- Företagsklass säkerhet och efterlevnad.

Dessa funktioner kräver nästan inga administration och alla tillhandahålls utan extra kostnad. De kan du fokusera på snabb apputveckling och accelerera din tid till marknad snarare än allokera värdefull tid och resurser för att hantera virtuella datorer och infrastruktur. Du kan dessutom fortsätta att utveckla ditt program med öppen källkod verktyg och plattform som du väljer att ge med snabbare och mer effektiv dina affärsbehov, allt utan att behöva veta nya kunskaper.

![Azure-databas för MySQL konceptuellt diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Den här artikeln ger en introduktion till Azure-databas för MySQL nyckelkoncept och funktioner som är relaterade till prestanda, skalbarhet och hanterbarhet, med länkar till mer detaljerad information. Se dessa Snabbstart för att komma igång:
- [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

För en uppsättning exempel som Azure CLI, se:
- [Azure CLI-exempel för Azure-databas för MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning inom några sekunder
Under förhandsgranskning, Azure-databas för MySQL-tjänst som erbjuder två tjänstnivåer: Basic och Standard. Varje nivå har olika prestanda och funktioner för att stödja lätta till tunga arbetsbelastningar. Du kan skapa din första app på en liten databas för några kronor i månaden och sedan justera skalan för att uppfylla behoven i din lösning. Dynamisk skalbarhet gör databasen besvarar transparent snabbt ändra resurskraven. Du betalar bara för resurserna du behöver och endast när det behövs. Se [prisnivåer](concepts-service-tiers.md) mer information.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Hur du bestämmer dig för när ska man reglerar upp eller ner? Du kan använda inbyggda prestanda övervakning och avisering funktioner, kombinerat med klassificeringarna prestanda baserat på Compute-enheter. Använda dessa verktyg kan du bedöma snabbt effekten av Compute enheter skala upp eller ned baserat på din aktuella eller planerade prestandabehov. Se [aviseringar](howto-alert-on-metric.md) mer information.

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande 99,99% tillgänglighet servicenivåavtal (SLA) tillhandahålls av ett globalt nätverk av Microsoft-hanterade Datacenter gör att din app igång 24/7. Med varje Azure-databas för MySQL-server kan du dra nytta av inbyggd säkerhet, feltolerans och dataskydd som du annars skulle behöva köpa eller utforma, skapa och hantera. Du kan använda point-in-time-återställning med Azure-databas för MySQL för att återställa en server till ett tidigare tillstånd, så långt tillbaka som 35 dagar.

## <a name="secure-your-data"></a>Skydda dina data
Tjänster för Azure-databas har en tradition av säkerhet för data som Azure-databas för MySQL upprätthåller, med funktioner som begränsar åtkomst, skydda data i vila och under rörelse och hjälper dig att övervaka aktiviteten. Besök den [Azure Säkerhetscenter](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx) information om azures plattformssäkerhet.

Azure-databas för MySQL-tjänst som använder kryptering för data i vila. Data, inklusive säkerhetskopior, krypteras på disken (med undantag av tillfälliga filer som skapas av motorn när du kör frågor). Tjänsten använder AES 256-bitars chiffer som ingår i Azure storage kryptering och nycklarna är hanteras av datorn. Lagringskryptering är alltid igång och kan inte inaktiveras.

Standard Azure-databasen för MySQL-tjänst som är konfigurerad för att kräva [SSL anslutningssäkerhet](./concepts-ssl-connection-security.md) för data i rörelse över nätverket. Att framtvinga SSL-anslutningar mellan databasservern och ditt klientprogram hjälper för att skydda mot ”man i mitten” attacker genom att kryptera dataströmmen mellan servern och ditt program.  Alternativt kan inaktivera du kräver SSL för att ansluta till din databastjänst om klientprogrammet inte har stöd för SSL-anslutning.

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till Azure-databas för MySQL och besvarat frågan ”vad är Azure-databas för MySQL”?, är du redo att:
- Se prissättningssidan för kostnadsjämförelse och Kostnadsberäknare. [Prissättning](https://azure.microsoft.com/pricing/details/mysql/)
- Kom igång genom att skapa den första servern. [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Skapa din första app med hjälp av språket du föredrar: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [gå](./connect-go.md)
