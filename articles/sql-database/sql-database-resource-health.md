---
title: Använd Azure Resource Health för att övervaka databasens hälsotillstånd
description: Använd Azure Resource Health för att övervaka SQL Database-hälsotillstånd, hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina SQL-resurser.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208875"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Använda Resource Health för att felsöka Azure SQL Database

## <a name="overview"></a>Översikt

[Resource Health](../service-health/resource-health-overview.md#get-started) för SQL Database hjälper dig att diagnostisera och få support när ett Azure-problem påverkar dina SQL-resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.

![Översikt](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Hälsokontroller

Resource Health avgör hälsotillståndet för din SQL-resurs genom att undersöka lyckades och fel på inloggningar till resursen. För närvarande undersöker Resource Health för din SQL DB-resurs endast inloggningsfel på grund av systemfel och inte användarfel. Resurshälsostatusen uppdateras var 1-2 minut.

## <a name="health-states"></a>Hälsostater

### <a name="available"></a>Tillgängligt

Statusen **Tillgänglig** innebär att Resource Health inte har upptäckt inloggningsfel på grund av systemfel på SQL-resursen.

![Tillgängligt](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degraderad

En status **för Degraded** innebär att Resource Health har upptäckt en majoritet av lyckade inloggningar, men vissa fel också. Dessa är troligen tillfälliga inloggningsfel. Om du vill minska effekten av anslutningsproblem som orsakas av tillfälliga inloggningsfel implementerar du [logiken för återförsök](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) i koden.

![Degraderad](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Inte tillgänglig

Statusen **Inte tillgänglig** innebär att Resource Health har upptäckt konsekventa inloggningsfel till din SQL-resurs. Om din resurs förblir i det här tillståndet under en längre tid kontaktar du supporten.

![Inte tillgänglig](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Okänt

Hälsotillståndet **för Okänd** anger att Resource Health inte har fått information om den här resursen på mer än 10 minuter. Även om den här statusen inte är en slutgiltig indikation på resursens tillstånd är den en viktig datapunkt i felsökningsprocessen. Om resursen körs som förväntat ändras resursens status till Tillgänglig efter några minuter. Om du har problem med resursen kan den okända hälsostatusen tyna på att en händelse på plattformen påverkar resursen.

![Okänt](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historisk information

Du kan komma åt upp till 14 dagars hälsohistorik i avsnittet Hälsohistorik i Resurshälsa. Avsnittet innehåller också driftstoppsorsaken (när den är tillgänglig) för de driftstopp som rapporteras av Resource Health. För närvarande visar Azure avbrottstid för din SQL-databasresurs med upp till två minuters kornighet. Den faktiska avbrottstiden är förmodligen mindre än en minut – genomsnittet är 8 sekunder.

### <a name="downtime-reasons"></a>Orsaker till driftstopp

När SQL Database har driftstopp utförs analys för att fastställa en orsak. När det är tillgängligt rapporteras driftstoppsorsaken i avsnittet Hälsohistorik i Resurshälsa. Orsaker till stillestånd publiceras vanligen 30 minuter efter en händelse.

#### <a name="planned-maintenance"></a>Planerat underhåll

Azure-infrastrukturen utför regelbundet planerat underhåll – uppgradering av maskinvaru- eller programvarukomponenter i datacentret. Medan databasen genomgår underhåll kan SQL avsluta vissa befintliga anslutningar och neka nya. Inloggningsfel som uppstår under planerat underhåll är vanligtvis tillfälliga och [logiken för återförsök](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) bidrar till att minska effekten. Om du fortsätter att uppleva inloggningsfel, vänligen kontakta supporten.

#### <a name="reconfiguration"></a>Omkonfiguration

Omkonfigurationer betraktas som tillfälliga förhållanden och förväntas från tid till annan. Dessa händelser kan utlösas genom belastningsutjämning eller programvaru-/maskinvarufel. Alla klientproduktionsprogram som ansluter till en molndatabas bör implementera en robust logik för [återförsök](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)för anslutning, eftersom det skulle bidra till att minska dessa situationer och i allmänhet göra felen transparenta för slutanvändaren.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [logik för återförsök för tillfälliga fel](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Felsöka, diagnostisera och förhindra SQL-anslutningsfel](./sql-database-connectivity-issues.md)
- Läs mer om [hur du konfigurerar resurshälsoaviseringar](../service-health/resource-health-alert-arm-template-guide.md)
- Få en översikt över [Resurshälsa](../service-health/resource-health-overview.md)
- [Vanliga frågor och svar om Resource Health](../service-health/resource-health-faq.md)
