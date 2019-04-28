---
title: Använd Azure Resource Health för att övervaka hälsan för SQL-databas | Microsoft Docs
description: Använd Azure Resource Health för att övervaka hälsan för SQL-databas, hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina SQL-resurser.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: c3b9fecd3ad404385732e55a9cf3aa65a6e388b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483064"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Använda Resource Health felsökning av anslutningar för Azure SQL Database

## <a name="overview"></a>Översikt

[Resource Health](../service-health/resource-health-overview.md#getting-started) för SQL Database hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina SQL-resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.

![Översikt](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Hälsokontroller

Resource Health avgör hälsotillståndet för din SQL-resurs genom att undersöka lyckade och misslyckade av inloggningar till resursen. Resource Health för SQL DB undersöker för närvarande endast misslyckade inloggningar på grund av systemfel och inte användarfel. Resource Health-status uppdateras var 1 – 2 minuter.

## <a name="health-states"></a>Hälsotillstånd

### <a name="available"></a>Tillgängligt

Statusen **tillgänglig** innebär att Resource Health inte har identifierat inloggningar fel på grund av fel i filsystemet på din SQL-resurs.

![Tillgängligt](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degraderad

Statusen **Degraderad** innebär att Resource Health har identifierat mestadels lyckade inloggningar, men också några misslyckade. Det här är mest sannolika tillfälliga inloggningsfel. För att minska effekten av anslutningsproblem orsakade av tillfälliga inloggningsfel kan implementera [logik för omprövning](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) i din kod.

![Degraderad](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Ej tillgänglig

Statusen **ej tillgänglig** innebär att Resource Health har identifierat konsekvent inloggningsförsök i din SQL-resurs. Om resursen är i det här tillståndet under en längre period, kontakta supporten.

![Ej tillgänglig](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Okänt

Hälsostatus **okänd** indikerar att Resource Health inte har fått information om den här resursen för mer än 10 minuter. Denna status inte är en slutgiltig indikation på resursens tillstånd, är det en viktig datapunkt i felsökningen. Om resursen körs som förväntat, ändras status för resursen till tillgänglig efter ett par minuter. Om du har problem med resursen kan okänd hälsostatus föreslå att en händelse i plattformen påverkar resursen.

![Inte tillgängligt](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historisk information

Du kan komma åt upp till 14 dagar efter hälsohistorik i avsnittet hälsotillstånd historik i Resource Health. Avsnittet innehåller också driftstopp orsaken (i förekommande fall) för stilleståndstider som rapporterats av Resource Health. Azure visar för närvarande avbrottstiden för din SQL database-resurs med en precision på två minuter. Den faktiska stilleståndstiden är förmodligen mindre än en minut – genomsnittlig 8s.

### <a name="downtime-reasons"></a>Nedtid orsaker

Analysen utförs för att fastställa en anledning när avbrott inträffar i din SQL-databas. När det är tillgängligt, rapporteras driftstopp orsaken i avsnittet Hälsohistorik i Resource Health. Orsaker till stillestånd publiceras vanligen 30 minuter efter en händelse.

#### <a name="planned-maintenance"></a>Planerat underhåll

Azure-infrastrukturen utför regelbundet planerat underhåll – uppgradering av maskin- eller komponenter i datacentret. När databasen genomgår underhåll, kan SQL avsluta vissa befintliga anslutningar och vägra nya. Misslyckade inträffade under planerat underhåll är vanligen övergående och [logik för omprövning](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) hjälper till att minska påverkan. Kontakta supporten om du fortsätter att uppleva fel vid inloggningen undviks.

#### <a name="reconfiguration"></a>Omkonfiguration

Reconfigurations betraktas som tillfälliga villkor och förväntas från tid till annan. De här händelserna kan utlösas av load balancing eller eller maskinvara fel. Produktion klientprogram som ansluter till en molndatabas bör implementera en robust anslutning [logik för omprövning](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors), eftersom den skulle hjälpa dig att undvika sådana situationer och bör Allmänt se felen transparent för slutanvändaren.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [logik för omprövning tillfälliga fel](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Felsöka, diagnostisera och förhindra SQL-anslutningsfel](./sql-database-connectivity-issues.md)
- Läs mer om [konfigurera Resource Health-aviseringar](../service-health/resource-health-alert-arm-template-guide.md)
- Få en översikt över [Resource Health](../service-health/resource-health-overview.md)
- [Resource Health vanliga frågor och svar](../service-health/resource-health-faq.md)
