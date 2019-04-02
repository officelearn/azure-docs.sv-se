---
title: Avancerade datasäkerhet – Azure SQL Database | Microsoft Docs
description: Läs mer om funktioner för att identifiera och klassificera känsliga data, hantera din databas säkerhetsrisker och identifiera avvikande aktiviteter som kan tyda på ett hot mot din Azure SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: monhaber
ms.reviewer: vanto
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: a078ac38cef5b395a19481188c474c7f908160d5
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791617"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Avancerad säkerhet för Azure SQL Database

Avancerad säkerhet är en enhetlig paketet för avancerade säkerhetsfunktioner i SQL. Den innehåller funktioner för att identifiera och klassificera känsliga data, lyfta upp och tjänstförvägransattacker databasen säkerhetsrisker, och identifiera avvikande aktiviteter som kan tyda på ett hot mot din databas. Det ger en samlad plats för aktivering och hantering av dessa funktioner.

## <a name="overview"></a>Översikt

Avancerade datasäkerhet (AD) innehåller en uppsättning avancerade funktioner för SQL security, inklusive dataidentifiering och klassificering, sårbarhetsbedömning och Avancerat skydd.

- [Dataidentifiering och klassificering](sql-database-data-discovery-and-classification.md) (för närvarande i förhandsversion) ger funktioner i Azure SQL Database för identifiering, klassificering, märkning och skydda känsliga data i dina databaser. Det kan användas för att ge insyn i tillståndet på din databasklassificering och för att spåra åtkomst till känsliga data i och utanför databasen.
- [Sårbarhetsbedömning](sql-vulnerability-assessment.md) är ett enkelt sätt att konfigurera tjänsten som kan upptäcka, spåra och hjälper dig att åtgärda säkerhetsrisker i databasen. Den ger inblick i dina säkerhetstillstånd och inkluderar lämpliga åtgärder för att lösa säkerhetsproblem och förbättra databasens skydd.
- [Avancerat skydd](sql-database-threat-detection-overview.md) identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databasen. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhetsaviseringar om potentiella säkerhetsproblem, SQL-inmatningsattacker samt avvikande åtkomstmönster i databasen. Avancerat skydd aviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärder att undersöka och åtgärda hot.

Aktivera SQL-ANNONSER när om du vill aktivera alla dessa funktioner som ingår. Med ett klick får du kan aktivera ANNONSER för alla databaser på din SQL Database-server eller hanterad instans. Aktivera eller hantera inställningar för ANNONSER kräver tillhör den [SQL-säkerhetsansvarig](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) rollen, administratörsroll för SQL database eller SQL server-administratörsroll. 

ANNONSER priser överensstämmer med Azure Security Center standard-nivån, där varje skyddad SQL Database-server eller hanterad instans räknas som en nod. Nyligen skyddade resurser kvalificera dig för en kostnadsfri utvärderingsversion av Security Center standard-nivån. Mer information finns i den [sidan med priser för Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Komma igång med Active Directory

Följande steg hjälper dig igång med Active Directory.

## <a name="1-enable-ads"></a>1. Aktivera ANNONSER

Aktivera ANNONSER genom att gå till **avancerade datasäkerhet** under den **Security** för din SQL Database-server eller hanterade instans. Aktivera ANNONSER för alla databaser på databasservern eller hanterad instans, klicka på **aktivera avancerade datasäkerhet på servern**.

> [!NOTE]
> Ett lagringskonto skapas automatiskt och konfigurerats för att lagra dina **Sårbarhetsbedömning** skanningsresultat. Om du har redan aktiverat ANNONSER för en annan server i samma resursgrupp och region, används det befintliga lagringskontot.

![Aktivera ANNONSER](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Kostnaden för ANNONSER justeras med Azure Security Center standard-nivån prissättning per nod, där en nod är hela SQL Database-server eller hanterad instans. Du därför betalar bara en gång för att skydda alla databaser på databasservern eller hanterad instans med ANNONSER. Du kan testa ANNONSER från början med en kostnadsfri utvärderingsversion.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Börja klassificera data, spåra säkerhetsrisker och undersöka hotaviseringar

Klicka på den **Dataidentifiering och klassificering** kort för att visa rekommenderade känsliga kolumner att klassificera och för att klassificera dina data med beständiga känslighet etiketter. Klicka på den **Sårbarhetsbedömning** kort att visa och hantera sårbarhetsgenomsökningar och rapporter och spåra din säkerhet datasekretesstandarder. Om säkerhetsaviseringar har tagits emot, klickar du på den **Advanced Threat Protection** kort att visa information om av aviseringarna och för att se en sammanfattande rapport med alla aviseringar i Azure-prenumerationen via sidan för security-aviseringar i Azure Security Center .

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Hantera inställningar för ANNONSER på din SQL Database-server eller hanterad instans

Om du vill visa och hantera inställningar för ANNONSER, gå till **avancerad säkerhet för Data** under den **Security** för din SQL Database-server eller hanterad instans. På den här sidan kan du aktivera eller inaktivera ANNONSER och ändra vulnerability assessment och inställningarna för avancerat skydd för hela SQL Database-server eller hanterad instans.

![Serverinställningar](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Hantera ANNONSER inställningar för en SQL-databas

Om du vill åsidosätta ADS-inställningar för en viss databas, kontrollera den **aktivera avancerad säkerhet för Data på databasnivå** kryssrutan. Använd det här alternativet bara om du har ett särskilt krav att ta emot separata aviseringar för avancerat skydd eller vulnerability assessment resultat för enskilda databasen, i stället för eller förutom aviseringar och resultaten som tagits emot för alla databaser på den databasservern eller hanterad instans.

Du kan sedan konfigurera de relevanta inställningarna för den här databasen när kryssrutan är markerad.
 
![Inställningar för databasen och Avancerat skydd](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Avancerade säkerhetsinställningar för din databasserver eller hanterad instans kan även nås från fönstret ADS-databasen. Klicka på **inställningar** i huvudfönstret för ANNONSER och klicka sedan på **serverinställningar för Visa avancerad datasäkerhet**. 

![Databasinställningar](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Nästa steg 

- Läs mer om [dataidentifiering och klassificering](sql-database-data-discovery-and-classification.md) 
- Läs mer om [utvärdering av säkerhetsrisker](sql-vulnerability-assessment.md) 
- Läs mer om [Avancerat skydd](sql-database-threat-detection.md)
- Läs mer om [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro)
