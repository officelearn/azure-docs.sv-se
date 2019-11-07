---
title: Använd Azure Resource Health för att övervaka SQL Database hälsa
description: Använd Azure Resource Health för att övervaka SQL Database hälsa, hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina SQL-resurser.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 10ce2da8ae8bcd8a12f0972d437a84a37a8390b9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687501"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Använd Resource Health för att felsöka anslutningarna för Azure SQL Database

## <a name="overview"></a>Översikt

[Resource Health](../service-health/resource-health-overview.md#get-started) för SQL Database hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina SQL-resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.

![Översikt](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Hälso kontroller

Resource Health fastställer hälso tillståndet för din SQL-resurs genom att undersöka lyckade och misslyckade inloggningar till resursen. För närvarande undersöker Resource Health för din SQL DB-resurs bara inloggnings fel på grund av systemfel och inte användar fel. Resource Healths status uppdateras var 1-2: e minut.

## <a name="health-states"></a>Hälso tillstånd

### <a name="available"></a>Tillgängligt

Statusen **tillgänglig** innebär att Resource Health inte har identifierat inloggnings fel på grund av systemfel på SQL-resursen.

![Tillgängligt](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degraderad

Statusen **Degraderad** innebär att Resource Health har identifierat mestadels lyckade inloggningar, men också några misslyckade. Detta är de flesta sannolika tillfälliga inloggnings fel. För att minska effekten av anslutnings problem som orsakas av tillfälliga inloggnings fel ska du implementera [logik för omförsök](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) i koden.

![Degraderad](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Inte tillgänglig

Statusen **otillgänglig** innebär att Resource Health har identifierat konsekventa inloggnings försök till din SQL-resurs. Kontakta supporten om resursen är i detta tillstånd under en längre tid.

![Inte tillgänglig](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Okänt

Hälso tillståndet för **okänd** indikerar att Resource Health inte har tagit emot information om den här resursen i mer än 10 minuter. Även om denna status inte är en slutgiltig indikation på resursens tillstånd, är det en viktig data punkt i fel söknings processen. Om resursen körs som förväntat ändras statusen för resursen till tillgänglig efter några minuter. Om du har problem med resursen kan den okända hälso statusen föreslå att en händelse i plattformen påverkar resursen.

![Okänt](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historisk information

Du kan komma åt upp till 14 dagar hälso historik i avsnittet hälso historik i Resource Health. Avsnittet innehåller även orsaken till stillestånds tiden (när det är tillgängligt) för de stillestånds tider som rapporteras av Resource Health. För närvarande visar Azure avbrottstid för din SQL-databasresurs med upp till två minuters kornighet. Den faktiska avbrottstiden är förmodligen mindre än en minut – genomsnittet är 8 sekunder.

### <a name="downtime-reasons"></a>Orsaker till stillestånds tid

När din SQL Database upplever stillestånds tid, utförs analysen för att fastställa en orsak. När det är tillgängligt rapporteras stillestånds orsaken i avsnittet hälso historik i Resource Health. Orsaker till stillestånd publiceras vanligen 30 minuter efter en händelse.

#### <a name="planned-maintenance"></a>Planerat underhåll

Azure-infrastrukturen utför regelbundet planerat underhåll – uppgradering av maskin-eller program varu komponenter i data centret. Medan databasen genomgår underhåll kan SQL avsluta vissa befintliga anslutningar och neka nya. De inloggnings fel som uppstod under planerat underhåll är vanligt vis tillfälliga och [nya omprövnings logik](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) bidrar till att minska påverkan. Om du fortsätter att uppleva inloggnings fel kan du kontakta supporten.

#### <a name="reconfiguration"></a>Omkonfiguration

Omkonfigurationer betraktas som tillfälliga villkor och förväntas av tiden. Dessa händelser kan utlösas av belastnings utjämning eller program varu-eller maskin varu problem. Alla klient produktions program som ansluter till en moln databas bör implementera en robust logik för anslutnings [försök](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors), eftersom det skulle hjälpa till att minimera dessa situationer och bör vanligt vis göra felen transparent för slutanvändaren.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [logik för återförsök för tillfälliga fel](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Felsöka, diagnostisera och förhindra SQL-anslutningsfel](./sql-database-connectivity-issues.md)
- Läs mer om hur du [konfigurerar Resource Health aviseringar](../service-health/resource-health-alert-arm-template-guide.md)
- Få en översikt över [Resource Health](../service-health/resource-health-overview.md)
- [Resource Health vanliga frågor och svar](../service-health/resource-health-faq.md)
