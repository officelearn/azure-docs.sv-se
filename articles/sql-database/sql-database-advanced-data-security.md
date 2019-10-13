---
title: Avancerad data säkerhet – Azure SQL Database | Microsoft Docs
description: Lär dig mer om funktioner för att identifiera och klassificera känsliga data, hantera dina databas sårbarheter och identifiera avvikande aktiviteter som kan tyda på ett hot mot din Azure SQL-databas.
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
ms.openlocfilehash: a8b380355d7eee6b79bff01d29160fdd3b42800d
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285811"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Avancerad data säkerhet för Azure SQL Database

Avancerad data säkerhet är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Den innehåller funktioner för att identifiera och klassificera känsliga data, Visa och minska eventuella säkerhets risker i databasen och identifiera avvikande aktiviteter som kan tyda på ett hot mot databasen. Det ger en samlad plats för aktivering och hantering av dessa funktioner.

## <a name="overview"></a>Översikt

Avancerad data säkerhet (ADS) innehåller en uppsättning avancerade SQL-säkerhetsfunktioner, inklusive data identifiering & klassificering, sårbarhets bedömning och Avancerat skydd.

- [Data identifierings & klassificering](sql-database-data-discovery-and-classification.md) (för närvarande i för hands version) innehåller funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera och märka & skydda känsliga data i dina databaser. Det kan användas för att ge insyn i tillståndet på din databasklassificering och för att spåra åtkomst till känsliga data i och utanför databasen.
- [Sårbarhets bedömning](sql-vulnerability-assessment.md) är ett enkelt sätt att konfigurera en tjänst som kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. Den ger inblick i dina säkerhetstillstånd och inkluderar lämpliga åtgärder för att lösa säkerhetsproblem och förbättra databasens skydd.
- [Avancerat skydd](sql-database-threat-detection-overview.md) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databasen. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhetsaviseringar om potentiella säkerhetsproblem, SQL-inmatningsattacker samt avvikande åtkomstmönster i databasen. Aviseringar för avancerat skydd ger information om den misstänkta aktiviteten och rekommenderar åtgärder för att undersöka och minimera hotet.

Aktivera SQL-annonser en gång för att aktivera alla dessa inkluderade funktioner. Med ett klick kan du aktivera annonser för alla databaser på din SQL Database Server eller en hanterad instans. Aktivering eller hantering av ADS-inställningar kräver tillhöra rollen [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) , rollen administratör för SQL-databas eller rollen SQL Server-administratör. 

Prissättningen för ANNONSERas med Azure Security Center standard nivå, där varje skyddad SQL Database-Server eller hanterad instans räknas som en nod. Nyligen skyddade resurser är berättigade till en kostnads fri utvärderings version av Security Center standard nivån. Mer information finns på sidan med [Azure Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Komma igång med annonser

Följande steg hjälper dig att komma igång med annonser.

## <a name="1-enable-ads"></a>1. Aktivera annonser

Aktivera annonser genom att gå till **Avancerad data säkerhet** under **säkerhets** rubriken för SQL Database-servern eller hanterade-instansen. Om du vill aktivera annonser för alla databaser på databas servern eller en hanterad instans klickar du på **aktivera avancerad data säkerhet på servern**.

> [!NOTE]
> Ett lagrings konto skapas och konfigureras automatiskt för att lagra dina genomsöknings resultat för **sårbarhets bedömning** . Om du redan har aktiverat annonser för en annan server i samma resurs grupp och region används det befintliga lagrings kontot.

![Aktivera annonser](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Kostnaden för annonser justeras med Azure Security Center standard pris per nod, där en nod är hela SQL Database servern eller en hanterad instans. Du betalar därför bara en gång för att skydda alla databaser på databas servern eller den hanterade instansen med annonser. Du kan prova att börja med att börja använda ADS med en kostnads fri utvärderings version.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. börja klassificera data, spåra sårbarheter och undersöka hot aviseringar

Klicka på **klassificerings kortet data identifierings &** för att se rekommenderade känsliga kolumner för att klassificera och klassificera data med beständiga känslighets etiketter. Klicka på kortet för **sårbarhets bedömning** för att visa och hantera sårbarhets genomsökningar och rapporter och för att spåra din säkerhets datasekretesstandarder. Om säkerhets aviseringar har mottagits klickar du på kortet **Avancerat skydd** för att visa information om aviseringarna och visa en konsol IDE rad rapport om alla aviseringar i din Azure-prenumeration via sidan Azure Security Center säkerhets aviseringar.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. hantera ADS-inställningar på din SQL Database Server eller hanterad instans

Om du vill visa och hantera ADS-inställningar går du till **Avancerad data säkerhet** under **säkerhets** rubriken för din SQL Database Server eller en hanterad instans. På den här sidan kan du aktivera eller inaktivera annonser och ändra inställningarna för sårbarhets bedömning och Avancerat skydd för hela SQL Database servern eller den hanterade instansen.

![Server inställningar](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. hantera ADS-inställningar för en SQL-databas

Om du vill åsidosätta ADS-inställningarna för en viss databas markerar du kryss rutan **aktivera avancerad data säkerhet på databas nivå** . Använd bara det här alternativet om du har ett visst krav för att ta emot separata varningar om Avancerat skydd eller sårbarhets bedömning för den enskilda databasen, i stället för de aviseringar och resultat som tagits emot för alla databaser på databas server eller hanterad instans.

När kryss rutan är markerad kan du konfigurera de relevanta inställningarna för den här databasen.
 
![Inställningar för databas-och Avancerat skydd](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Avancerade data säkerhets inställningar för din databas server eller hanterad instans kan också nås från fönstret ADS Database. Klicka på **Inställningar** i rutan huvud annonser och klicka sedan på **Visa avancerade data säkerhets Server inställningar**. 

![Databas inställningar](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Nästa steg 

- Läs mer om [data identifiering & klassificering](sql-database-data-discovery-and-classification.md) 
- Läs mer om [sårbarhets bedömning](sql-vulnerability-assessment.md) 
- Läs mer om [Avancerat skydd](sql-database-threat-detection.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
