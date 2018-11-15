---
title: Översikt över Azure Database för MySQL relationsdatabastjänst
description: Översikt över tjänsten Azure Database for MySQL relationsdatabas.
services: mysql
ms.service: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.custom: mvc
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 4d0d78b978f6454170b52a95f538f143b9f24505
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684808"
---
# <a name="what-is-azure-database-for-mysql"></a>Vad är Azure Database for MySQL?
Azure Database för MySQL är en relationsdatabastjänst i Microsoft-molnet baserat på den [MySQL Community Edition](https://www.mysql.com/products/community/) database engine, version 5.6 och 5.7. Azure Database för MySQL ger:

- Inbyggd hög tillgänglighet utan extra kostnad.
- Förutsägbar prestanda med hjälp av priser för användningsbaserad betalning.
- Skala efter behov inom några sekunder.
- Säkrad för att skydda känsliga data i vila och under rörelse.
- Automatisk säkerhetskopiering och återställning av tidpunkt för upp till 35 dagar.
- Säkerhet och efterlevnad i företagsklass.

Dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. De gör att du kan fokusera på snabb apputveckling och att accelerera din tid till marknaden, istället för att ägna värdefull tid och resurser åt att hantera virtuella datorer och infrastruktur. Du kan dessutom fortsätta att utveckla programmet med verktygen med öppen källkod och på valfri plattform, och leverera med den hastighet och effektivitet som verksamheten kräver utan att du behöver lära dig nya färdigheter.

![Azure Database for MySQL konceptuellt diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Den här artikeln är en introduktion till Azure Database för MySQL nyckelkoncept och funktioner som relaterar till prestanda, skalbarhet och hanterbarhet, med länkar till mer detaljerad information. Läs följande snabbstarter innan du börjar:
- [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

En uppsättning Azure CLI-exempel finns här:
- [Azure CLI-exempel för Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder
Azure Database for MySQL-tjänsten erbjuder flera tjänstnivåer: Basic, generell användning och Minnesoptimerad. Varje nivå erbjuder olika prestanda och funktioner för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Se [prisnivåer](concepts-service-tiers.md) mer information.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Hur avgör du när du ska reglera upp eller ner? Du kan använda de inbyggda funktionerna för prestandaövervakning och avisering, tillsammans med prestandaklassificering baserat på virtuella kärnor. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner de virtuella kärnorna baserat på dina aktuella eller beräknade prestandakrav. Mer information finns i [Aviseringar](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande serviceavtal (SLA) med 99,99 % tillgänglighet, drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång 24/7. Med varje Azure Database for MySQL-server kan dra du nytta av inbyggd säkerhet, feltolerans och dataskydd som du annars skulle behöva köpa eller designa, bygga och hantera. Du kan använda point-in-time-återställning med Azure Database for MySQL, för att återställa en server till ett tidigare skede, 35 dagar.

## <a name="secure-your-data"></a>Skydda dina data
Azure database-tjänster har en tradition av datasäkerhet som Azure Database for MySQL upprätthåller, med funktioner som begränsar åtkomst, skydda data i vila och under rörelse och hjälper dig att övervaka aktiviteten. Besök [Azure Säkerhetscenter](https://www.microsoft.com/en-us/trustcenter/security) för information om Azures plattformssäkerhet.

Azure Database for MySQL-tjänsten använder lagringskryptering för data i vila. Data, inklusive säkerhetskopior, krypteras på disk (med undantag för tillfälliga filer som skapas av motorn vid körning av frågor). Tjänsten använder chiffer med AES 256 bitar som ingår i Azures lagringskryptering, och nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras.

Som standard konfigureras Azure Database for MySQL-tjänsten för att kräva [SSL-anslutningssäkerhet](./concepts-ssl-connection-security.md) för data i rörelse över nätverket. Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet. Du kan även välja att inaktivera SSL-kravet för anslutning till databastjänsten om klientprogrammet inte har stöd för SSL-anslutning.

## <a name="contacts"></a>Contacts
För frågor eller förslag som du kan ha om hur du arbetar med Azure Database för MySQL, skicka ett e-postmeddelande till Azure Database för MySQL-teamet ([ @Ask Azure DB för MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Observera att detta inte är ett alias för teknisk support.

Tänk dessutom på följande kontaktpunkter efter behov:
- Kontakta Azure Support genom att [skicka in ett supportärende från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till Azure Database för MySQL och besvarat frågan ”vad är Azure Database för MySQL”?, är du redo att:
- På prissättningssidan finns kostnadsjämförelser och kostnadsberäknare. [Prissättning](https://azure.microsoft.com/pricing/details/mysql/)
- Kom igång genom att skapa din första server. [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Skapa din första app med det språk du föredrar: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [gå](./connect-go.md)
