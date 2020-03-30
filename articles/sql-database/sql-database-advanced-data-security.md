---
title: Avancerad datasäkerhet
description: Lär dig mer om funktioner för att identifiera och klassificera känsliga data, hantera databassårbarheter och identifiera avvikande aktiviteter som kan tyda på ett hot mot din Azure SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: 1f0e6694e596dc60264dfe0789a2f80090e0da3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269138"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Avancerad datasäkerhet för Azure SQL Database

Avancerad datasäkerhet är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Det innefattar funktioner för att identifiera och klassificera känsliga data, lyfta fram och åtgärda potentiella säkerhetsrisker i databasen och identifiera avvikande aktiviteter som kan indikera ett hot mot databasen. Det ger en samlad plats för aktivering och hantering av dessa funktioner.

## <a name="overview"></a>Översikt

Avancerad datasäkerhet (ADS) innehåller en uppsättning avancerade SQL-säkerhetsfunktioner, inklusive dataidentifiering & klassificering, sårbarhetsbedömning och avancerat skydd mot hot.

- [Klassificering & &](sql-database-data-discovery-and-classification.md) innehåller funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera, märka & skydda känsliga data i dina databaser. Det kan användas för att ge insyn i tillståndet på din databasklassificering och för att spåra åtkomst till känsliga data i och utanför databasen.
- [Sårbarhetsbedömning](sql-vulnerability-assessment.md) är en enkel att konfigurera tjänst som kan identifiera, spåra och hjälpa dig att åtgärda potentiella databassårbarheter. Den ger inblick i dina säkerhetstillstånd och inkluderar lämpliga åtgärder för att lösa säkerhetsproblem och förbättra databasens skydd.
- [Advanced Threat Protection](sql-database-threat-detection-overview.md) identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din databas. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhetsaviseringar om potentiella säkerhetsproblem, SQL-inmatningsattacker samt avvikande åtkomstmönster i databasen. Advanced Threat Protection-aviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minska risken.

Aktivera SQL ADS en gång för att aktivera alla dessa inkluderade funktioner. Med ett klick kan du aktivera ADS för alla databaser på SQL Database-servern eller hanterad instans. För att aktivera eller hantera ADS-inställningar krävs att den hör till rollen [SQL Security Manager,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) SQL-databasadministratörsrollen eller SQL-serveradministratörsrollen. 

ADS-prissättningen stämmer överens med Azure Security Center-standardnivå, där varje skyddad SQL Database-server eller hanterad instans räknas som en nod. Nyligen skyddade resurser kvalificerar sig för en kostnadsfri utvärderingsversion av Security Center-standardnivån. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/)för Azure Security Center .

## <a name="getting-started-with-ads"></a>Komma igång med ADS

Följande steg kommer igång med ADS.

## <a name="1-enable-ads"></a>1. Aktivera ADS

Aktivera ADS genom att navigera till **Avancerad datasäkerhet** under rubriken **Säkerhet** för SQL Database-servern eller hanterad instans. Om du vill aktivera ADS för alla databaser på databasservern eller hanterad instans klickar du på **Aktivera avancerad datasäkerhet på servern**.

> [!NOTE]
> Ett lagringskonto skapas och konfigureras automatiskt för att lagra dina undersökningsresultat **för sårbarhetsbedömning.** Om du redan har aktiverat ADS för en annan server i samma resursgrupp och region används det befintliga lagringskontot.

![Aktivera ADS](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Kostnaden för ADS är anpassad till Azure Security Center standardnivåpris per nod, där en nod är hela SQL Database-servern eller hanterad instans. Du betalar alltså bara en gång för att skydda alla databaser på databasservern eller hanterad instans med ADS. Du kan prova ADS ut från början med en gratis testperiod.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Börja klassificera data, spåra sårbarheter och undersöka hotvarningar

Klicka på klassificeringskortet **dataidentifiering &** om du vill visa rekommenderade känsliga kolumner för att klassificera och klassificera data med beständiga känslighetsetiketter. Klicka på kortet **Sårbarhetsbedömning** om du vill visa och hantera sårbarhetssökningar och rapporter och för att spåra säkerhetsstorleken. Om säkerhetsaviseringar har tagits emot klickar du på kortet **Avancerat skydd mot hot** för att visa information om aviseringarna och för att se en konsoliderad rapport om alla aviseringar i din Azure-prenumeration via säkerhetsaviseringssidan för Azure Security Center.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Hantera ADS-inställningar på SQL Database-servern eller hanterad instans

Om du vill visa och hantera ADS-inställningar navigerar du till **Avancerad datasäkerhet** under rubriken **Säkerhet** för SQL Database-servern eller hanterad instans. På den här sidan kan du aktivera eller inaktivera ADS och ändra sårbarhetsbedömning och avancerade hotskyddsinställningar för hela SQL Database-servern eller hanterade instansen.

![Serverinställningar
](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Hantera ADS-inställningar för en SQL-databas

Om du vill åsidosätta ADS-inställningarna för en viss databas markerar du kryssrutan **Aktivera avancerad datasäkerhet på databasnivå.** Använd det här alternativet endast om du har ett särskilt krav på att få separata förhandsvarningar om skydd mot hot eller sårbarhetsbedömning för den enskilda databasen, i stället för eller utöver de aviseringar och resultat som tas emot för alla databaser i databasserver eller hanterad instans.

När kryssrutan är markerad kan du sedan konfigurera relevanta inställningar för den här databasen.
 
![Inställningar för databas och avancerat skydd mot hot](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Avancerade datasäkerhetsinställningar för databasservern eller den hanterade instansen kan också nås från ADS-databasfönstret. Klicka på **Inställningar** i huvudfönstret i ADS och sedan på **Visa avancerade serverinställningar för datasäkerhet**. 

![Databasinställningar](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Nästa steg 

- Läs mer om [klassificering & & dataidentifiering](sql-database-data-discovery-and-classification.md) 
- Läs mer om [sårbarhetsbedömning](sql-vulnerability-assessment.md) 
- Läs mer om [avancerat hotskydd](sql-database-threat-detection.md)
- Läs mer om [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro)
