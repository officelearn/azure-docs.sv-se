---
title: Översikt över Azure Database for MySQL Relations databas tjänst
description: Lär dig mer om tjänsten Azure Database for MySQL, en Relations databas tjänst i Microsoft-molnet baserat på MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 680b5e9ef8e7e8ed59d3b502b49fc1b45d016e80
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483516"
---
# <a name="what-is-azure-database-for-mysql"></a>Vad är Azure Database for MySQL?

Azure Database for MySQL är en Relations databas tjänst i Microsoft-molnet baserat på [MySQL Community Edition](https://www.mysql.com/products/community/) (tillgänglig i GPLv2-licensen) databas motor, version 5,6, 5,7 och 8,0. Azure Database for MySQL levererar:

- Inbyggd hög tillgänglighet utan extra kostnad.
- Förutsägbar prestanda med hjälp av priser för användningsbaserad betalning.
- Skala efter behov inom några sekunder.
- Säkrad för att skydda känsliga data i vila och under rörelse.
- Automatisk säkerhetskopiering och återställning av tidpunkt för upp till 35 dagar.
- Säkerhet och efterlevnad i företagsklass.

Dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. De gör att du kan fokusera på snabb apputveckling och att accelerera din tid till marknaden, istället för att ägna värdefull tid och resurser åt att hantera virtuella datorer och infrastruktur. Du kan dessutom fortsätta att utveckla programmet med verktygen med öppen källkod och på valfri plattform, och leverera med den hastighet och effektivitet som verksamheten kräver utan att du behöver lära dig nya färdigheter.

![Azure Database for MySQL konceptuellt diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Den här artikeln är en introduktion till Azure Database for MySQL grundläggande begrepp och funktioner relaterade till prestanda, skalbarhet och hanterbarhet, med länkar till information. Läs följande snabbstarter innan du börjar:

- [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

En uppsättning Azure CLI-exempel finns här:

- [Azure CLI-exempel för Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder
Tjänsten Azure Database for MySQL erbjuder flera tjänst nivåer: Basic, Generell användning och Minnesoptimerade. Varje nivå erbjuder olika prestanda och funktioner för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i  [Prisnivåer](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Hur avgör du när du ska reglera upp eller ner? Du kan använda de inbyggda funktionerna för prestandaövervakning och avisering, tillsammans med prestandaklassificering baserat på virtuella kärnor. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner de virtuella kärnorna baserat på dina aktuella eller beräknade prestandakrav. Mer information finns i [Aviseringar](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande serviceavtal (SLA) med 99,99 % tillgänglighet, drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång 24/7. Med varje Azure Database for MySQL server kan du dra nytta av inbyggd säkerhet, fel tolerans och data skydd som du annars skulle behöva köpa, utforma, utveckla och hantera. Med Azure Database for MySQL kan du använda återställning av tidpunkt för att återställa en server till ett tidigare tillstånd, så långt tillbaka som 35 dagar.

## <a name="secure-your-data"></a>Skydda dina data
Azure Database Services har en tradition av data säkerhet som Azure Database for MySQL har behållit, med funktioner som begränsar åtkomst, skyddar data i vila och i rörelse och hjälper dig att övervaka aktiviteten. Besök [Azure Säkerhetscenter](https://www.microsoft.com/trustcenter/security) för information om Azures plattformssäkerhet. Mer information om Azure Database for MySQL säkerhetsfunktioner finns i [säkerhets översikten](concepts-security.md).

## <a name="contacts"></a>Contacts
Om du har frågor eller förslag som du kan behöva för att arbeta med Azure Database for MySQL skickar du ett e-postmeddelande till Azure Database for MySQL-teamet ([@Ask Azure dB för MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Den här e-postadressen är inte ett alias för teknisk support.

Tänk dessutom på följande kontaktpunkter efter behov:

- Kontakta Azure Support genom att [skicka in ett supportärende från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till Azure Database for MySQL och besvarat frågan "Vad är Azure Database for MySQL?" du är redo att:

- På prissättningssidan finns kostnadsjämförelser och kostnadsberäknare. [Prissättning](https://azure.microsoft.com/pricing/details/mysql/)
- Kom igång genom att skapa din första server. [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Bygg din första app med önskat språk: [python](./connect-python.md) | [Node. js](./connect-nodejs.md) | [Java](./connect-java.md) | [ruby](./connect-ruby.md) | [php](./connect-php.md) | [.net (C#)](./connect-csharp.md) | [Go](./connect-go.md)
