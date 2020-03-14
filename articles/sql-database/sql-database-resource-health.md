---
title: Använd Azure Resource Health för att övervaka databas hälsa
description: Använd Azure Resource Health för att övervaka hälsan för SQL-databas, hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina SQL-resurser.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 9e19e904b47d69444b491dd88ffe49ff812aafc3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208875"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Använda Resource Health felsökning av anslutningar för Azure SQL Database

## <a name="overview"></a>Översikt

[Resource Health](../service-health/resource-health-overview.md#get-started) för SQL Database hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina SQL-resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.

![Översikt](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Hälsokontroller

Resource Health avgör hälsotillståndet för din SQL-resurs genom att undersöka lyckade och misslyckade av inloggningar till resursen. Resource Health för SQL DB undersöker för närvarande endast misslyckade inloggningar på grund av systemfel och inte användarfel. Resource Health-status uppdateras var 1 – 2 minuter.

## <a name="health-states"></a>Hälsotillstånd

### <a name="available"></a>Tillgängligt

Statusen **tillgänglig** innebär att Resource Health inte har identifierat inloggnings fel på grund av systemfel på SQL-resursen.

![Tillgängligt](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degraderad

Statusen **Degraderad** innebär att Resource Health har identifierat mestadels lyckade inloggningar, men också några misslyckade. Det här är mest sannolika tillfälliga inloggningsfel. För att minska effekten av anslutnings problem som orsakas av tillfälliga inloggnings fel ska du implementera [logik för omförsök](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) i koden.

![Degraderad](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Inte tillgänglig

Statusen **otillgänglig** innebär att Resource Health har identifierat konsekventa inloggnings försök till din SQL-resurs. Om resursen är i det här tillståndet under en längre period, kontakta supporten.

![Inte tillgänglig](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Okänt

Hälso tillståndet för **okänd** indikerar att Resource Health inte har tagit emot information om den här resursen i mer än 10 minuter. Denna status inte är en slutgiltig indikation på resursens tillstånd, är det en viktig datapunkt i felsökningen. Om resursen körs som förväntat, ändras status för resursen till tillgänglig efter ett par minuter. Om du har problem med resursen kan okänd hälsostatus föreslå att en händelse i plattformen påverkar resursen.

![Okänt](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historisk information

Du kan komma åt upp till 14 dagar efter hälsohistorik i avsnittet hälsotillstånd historik i Resource Health. Avsnittet innehåller också driftstopp orsaken (i förekommande fall) för stilleståndstider som rapporterats av Resource Health. För närvarande visar Azure avbrottstid för din SQL-databasresurs med upp till två minuters kornighet. Den faktiska avbrottstiden är förmodligen mindre än en minut – genomsnittet är 8 sekunder.

### <a name="downtime-reasons"></a>Nedtid orsaker

Analysen utförs för att fastställa en anledning när avbrott inträffar i din SQL-databas. När det är tillgängligt, rapporteras driftstopp orsaken i avsnittet Hälsohistorik i Resource Health. Orsaker till stillestånd publiceras vanligen 30 minuter efter en händelse.

#### <a name="planned-maintenance"></a>Planerat underhåll

Azure-infrastrukturen utför regelbundet planerat underhåll – uppgradering av maskin- eller komponenter i datacentret. När databasen genomgår underhåll, kan SQL avsluta vissa befintliga anslutningar och vägra nya. De inloggnings fel som uppstod under planerat underhåll är vanligt vis tillfälliga och [nya omprövnings logik](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) bidrar till att minska påverkan. Kontakta supporten om du fortsätter att uppleva fel vid inloggningen undviks.

#### <a name="reconfiguration"></a>Omkonfiguration

Reconfigurations betraktas som tillfälliga villkor och förväntas från tid till annan. De här händelserna kan utlösas av load balancing eller eller maskinvara fel. Alla klient produktions program som ansluter till en moln databas bör implementera en robust logik för anslutnings [försök](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors), eftersom det skulle hjälpa till att minimera dessa situationer och bör vanligt vis göra felen transparent för slutanvändaren.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [logik för återförsök för tillfälliga fel](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Felsöka, diagnostisera och förhindra SQL-anslutningsfel](./sql-database-connectivity-issues.md)
- Läs mer om hur du [konfigurerar Resource Health aviseringar](../service-health/resource-health-alert-arm-template-guide.md)
- Få en översikt över [Resource Health](../service-health/resource-health-overview.md)
- [Resource Health vanliga frågor och svar](../service-health/resource-health-faq.md)
