---
title: SQL Advanced datasäkerhet – Azure SQL Database | Microsoft Docs
description: Läs mer om funktioner för att identifiera och klassificera känsliga data, hantera din databas säkerhetsrisker och identifiera avvikande aktiviteter som kan tyda på ett hot mot din Azure SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 1/29/2019
ms.openlocfilehash: ae710d705ed843d59af0d0f9154aab467f01c4dd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468469"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Avancerad säkerhet för Azure SQL Database

SQL avancerade datasäkerhet är en enhetlig paketet för avancerade funktioner för SQL-säkerhet. Den innehåller funktioner för att identifiera och klassificera känsliga data, lyfta upp och tjänstförvägransattacker databasen säkerhetsrisker, och identifiera avvikande aktiviteter som kan tyda på ett hot mot din databas. Det ger en samlad plats för aktivering och hantering av dessa funktioner.

## <a name="overview"></a>Översikt

SQL avancerade Data Security (AD) innehåller en uppsättning avancerade funktioner för SQL security, inklusive Dataidentifiering och klassificering, Sårbarhetsbedömning och Hotidentifiering.

- [Data Discovery & Classification](sql-database-data-discovery-and-classification.md) (för närvarande i förhandsversion) ger funktioner som är inbyggda i Azure SQL Database för identifiering, klassificering, etikettering och skydd av känsliga data i databasen. Det kan användas för att ge insyn i tillståndet på din databasklassificering och för att spåra åtkomst till känsliga data i och utanför databasen.
- [Vulnerability Assessment](sql-vulnerability-assessment.md) är en tjänst som är enkel att konfigurera och som kan identifiera, spåra och hjälpa dig att åtgärda eventuella säkerhetsrisker i databasen. Den ger inblick i dina säkerhetstillstånd och inkluderar lämpliga åtgärder för att lösa säkerhetsproblem och förbättra databasens skydd.
- [Threat Detection](sql-database-threat-detection-overview.md) identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databasen. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhetsaviseringar om potentiella säkerhetsproblem, SQL-inmatningsattacker samt avvikande åtkomstmönster i databasen. Threat Detection-aviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minska risken.

Aktivera SQL-ANNONSER när om du vill aktivera alla dessa funktioner som ingår. Med ett klick får du kan aktivera ANNONSER för alla databaser på din SQL Database-server eller hanterad instans. Aktivera eller hantera inställningar för ANNONSER kräver tillhör den [SQL-säkerhetsansvarig](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) rollen, administratörsroll för SQL database eller SQL server-administratörsroll. 

ANNONSER priser överensstämmer med Azure Security Center standard-nivån, där varje skyddad SQL Database-server eller hanterad instans räknas som en nod. Nyligen skyddade resurser kvalificera dig för en kostnadsfri utvärderingsversion av Security Center standard-nivån. Mer information finns i den [sidan med priser för Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Komma igång med Active Directory

Följande steg hjälper dig igång med Active Directory.

## <a name="1-enable-ads"></a>1. Aktivera ANNONSER

Aktivera ANNONSER genom att gå till **avancerade datasäkerhet** under den **Security** för din SQL Database-server eller hanterade instans. Aktivera ANNONSER för alla databaser på databasservern eller hanterad instans, klicka på **aktivera avancerade datasäkerhet på servern**.

![Aktivera ANNONSER](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Kostnaden för ANNONSER justeras med Azure Security Center standard-nivån prissättning per nod, där en nod är hela SQL Database-server eller hanterad instans. Du därför betalar bara en gång för att skydda alla databaser på databasservern eller hanterad instans med ANNONSER. Du kan testa ANNONSER från början med en kostnadsfri utvärderingsversion.

## <a name="2-configure-vulnerability-assessment"></a>2. Konfigurera utvärdering av säkerhetsrisker

Om du vill börja använda Vulnerability Assessment, måste du konfigurera ett lagringskonto där sökresultat sparas. Om du vill göra det klickar du på kortet utvärdering av säkerhetsrisker.

![Konfigurera VA](./media/sql-advanced-protection/configure_va.png) 

Välj eller skapa ett lagringskonto för att spara sökresultat. Du kan också aktivera periodiskt återkommande sökningar för att konfigurera Sårbarhetsbedömning för att köra automatiska sökningar en gång i veckan. En sammanfattning för resultatet av genomsökningen skickas till de e-postadresser som du anger.

![VA-inställningar](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Börja klassificera data, spåra säkerhetsrisker och undersöka hotaviseringar

Klicka på den **Dataidentifiering och klassificering** kort för att visa rekommenderade känsliga kolumner att klassificera och för att klassificera dina data med beständiga känslighet etiketter. Klicka på den **Sårbarhetsbedömning** kort att visa och hantera sårbarhetsgenomsökningar och rapporter och spåra din säkerhet datasekretesstandarder. Om säkerhetsaviseringar har tagits emot, klickar du på den **Hotidentifiering** kort att visa information om av aviseringarna och för att se en sammanfattande rapport med alla aviseringar i Azure-prenumerationen via sidan för security-aviseringar i Azure Security Center.

## <a name="4-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>4. Hantera inställningar för ANNONSER på din SQL Database-server eller hanterad instans

Om du vill visa och hantera inställningarna för avancerad säkerhet för Data, gå till **avancerade datasäkerhet** under den **Security** för din SQL Database-server eller hanterad instans. På den här sidan kan du aktivera eller inaktivera ANNONSER och ändra Hotidentifiering inställningar för hela SQL Database-server eller hanterad instans.

![Serverinställningar](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-ads-settings-for-a-sql-database"></a>5. Hantera ANNONSER inställningar för en SQL-databas

Om du vill åsidosätta inställningarna för Hotidentifiering för ANNONSER för en viss databas, kontrollera den **aktivera avancerad säkerhet för Data på databasnivå** kryssrutan. Använd det här alternativet bara om du har ett särskilt krav att ta emot separata hot identifiering aviseringar för enskilda databasen, i stället för eller förutom aviseringarna som togs emot för alla databaser på databasservern eller hanterad instans. 

När kryssrutan är markerad, klickar du på **hotidentifieringsinställningarna för den här databasen** och konfigurera relevanta inställningar för den här databasen.

![Identifieringsinställningar i databasen och hot](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Avancerade säkerhetsinställningar i Data för din databasserver eller hanterad instans kan även nås från fönstret ADS-databasen. Klicka på **inställningar** i huvudfönstret för ANNONSER och klicka sedan på **serverinställningar för Visa avancerad datasäkerhet**. 

![Databasinställningar](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Nästa steg 

- Läs mer om [Dataidentifiering och klassificering](sql-database-data-discovery-and-classification.md) 
- Läs mer om [utvärdering av säkerhetsrisker](sql-vulnerability-assessment.md) 
- Läs mer om [Hotidentifiering](sql-database-threat-detection.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
