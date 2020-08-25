---
title: Översikt – Azure Database for MySQL
description: Lär dig mer om tjänsten Azure Database for MySQL, en Relations databas tjänst i Microsoft-molnet baserat på MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 37bc99d9f83f185a5372fd45634351987b85e20b
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2020
ms.locfileid: "88763668"
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

## <a name="automated-patching"></a>Automatisk uppdatering
Tjänsten utför automatisk uppdatering av underliggande maskin vara, operativ system och databas motor. Korrigeringen innehåller säkerhets-och program uppdateringar för den underliggande maskin varan, operativ systemet och databas motorn. För MySQL-motorn är lägre versions uppgraderingar automatiska och ingår som en del av korrigerings versionen. När communityn släpper en lägre version, integreras den automatiskt som en del av test cykeln för tjänsten. Testningen av den lägre versionen utförs på några av de kanoniska arbets belastningarna för MySQL. Den lägre versionen av MySQL-motorn utvärderas för tillförlitlighet (inga krascher), tillgänglighet, säkerhet och prestanda. Det är inte alla lägre versioner som släpps till produktion i tjänsten, men det är utvärderas baserat på den kritiska fel korrigeringen och ett nytt öknings värde. Detta är att göra den högra balansen mellan nytt ökande värde och minimera variablerna i systemet för stabilitet. Det finns inga användar åtgärder eller konfigurations inställningar som krävs för korrigering. Uppdaterings frekvensen är tjänst som hanteras baserat på nytto lastens allvarlighets grad. I allmänhet följer tjänsten varje månads versions schema som en del av den kontinuerliga integreringen och versionen. Användare kan prenumerera på ett [planerat underhålls meddelande](concepts-monitoring.md) för att få ett meddelande om kommande underhåll 72 timmar före händelsen.

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder
Tjänsten Azure Database for MySQL erbjuder flera tjänst nivåer: Basic, Generell användning och Minnesoptimerade. Varje nivå erbjuder olika prestanda och funktioner för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i  [Prisnivåer](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Hur avgör du när du ska reglera upp eller ner? Du kan använda de inbyggda funktionerna för prestandaövervakning och avisering, tillsammans med prestandaklassificering baserat på virtuella kärnor. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner de virtuella kärnorna baserat på dina aktuella eller beräknade prestandakrav. Mer information finns i [Aviseringar](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande serviceavtal (SLA) med 99,99 % tillgänglighet, drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång 24/7. Med varje Azure Database for MySQL server kan du dra nytta av inbyggd säkerhet, fel tolerans och data skydd som du annars skulle behöva köpa, utforma, utveckla och hantera. Med Azure Database for MySQL kan du använda återställning av tidpunkt för att återställa en server till ett tidigare tillstånd, så långt tillbaka som 35 dagar.

## <a name="secure-your-data"></a>Skydda dina data
Azure Database Services har en tradition av data säkerhet som Azure Database for MySQL har behållit, med funktioner som begränsar åtkomst, skyddar data i vila och i rörelse och hjälper dig att övervaka aktiviteten. Besök [Azure Säkerhetscenter](https://www.microsoft.com/trustcenter/security) för information om Azures plattformssäkerhet. Mer information om Azure Database for MySQL säkerhetsfunktioner finns i [säkerhets översikten](concepts-security.md).

## <a name="contacts"></a>Kontakter
Om du har frågor eller förslag som du kan behöva för att arbeta med Azure Database for MySQL skickar du ett e-postmeddelande till Azure Database for MySQL-teamet ([ @Ask Azure dB för MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Den här e-postadressen är inte ett alias för teknisk support.

Tänk dessutom på följande kontaktpunkter efter behov:

- Kontakta Azure Support genom att [skicka in ett supportärende från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till Azure Database for MySQL och besvarat frågan "Vad är Azure Database for MySQL?" du är redo att:

- På prissättningssidan finns kostnadsjämförelser och kostnadsberäknare. [Prissättning](https://azure.microsoft.com/pricing/details/mysql/)
- Kom igång genom att skapa din första server. [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Bygg din första app med önskat språk:
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Kör](./connect-go.md)
