---
title: Avancerat skydd – Azure SQL Database | Microsoft Docs
description: Läs mer om funktioner för att identifiera och klassificera känsliga data, hantera din databas säkerhetsrisker och identifiera avvikande aktiviteter som kan tyda på ett hot mot din Azure SQL-databas.
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 5/17/2018
ms.author: ronitr
ms.reviewer: carlrab
ms.openlocfilehash: 8881f66a73a2368696ee16cc0f9bb0b16d2f6d7e
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054130"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Avancerat skydd för Azure SQL Database

SQL Advanced Threat Protection är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Den innehåller funktioner för att identifiera och klassificera känsliga data, lyfta upp och tjänstförvägransattacker databasen säkerhetsrisker, och identifiera avvikande aktiviteter som kan tyda på ett hot mot din databas. Det ger en samlad plats för aktivering och hantering av dessa funktioner. 

## <a name="overview"></a>Översikt

SQL avancerade Threat Protection (ATP) tillhandahåller en uppsättning avancerade funktioner för SQL security, inklusive Dataidentifiering och klassificering, Sårbarhetsbedömning och Hotidentifiering. 

- [Data Discovery & Classification](sql-database-data-discovery-and-classification.md) (för närvarande i förhandsversion) ger funktioner som är inbyggda i Azure SQL Database för identifiering, klassificering, etikettering och skydd av känsliga data i databasen. Det kan användas för att ge insyn i tillståndet på din databasklassificering och för att spåra åtkomst till känsliga data i och utanför databasen.
- [Vulnerability Assessment](sql-vulnerability-assessment.md) är en tjänst som är enkel att konfigurera och som kan identifiera, spåra och hjälpa dig att åtgärda eventuella säkerhetsrisker i databasen. Den ger inblick i dina säkerhetstillstånd och inkluderar lämpliga åtgärder för att lösa säkerhetsproblem och förbättra databasens skydd.
- [Threat Detection](sql-database-threat-detection.md) identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databasen. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhetsaviseringar om potentiella säkerhetsproblem, SQL-inmatningsattacker samt avvikande åtkomstmönster i databasen. Threat Detection-aviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minska risken.

Aktivera SQL ATP när om du vill aktivera alla dessa funktioner som ingår. Med en enda klickning kan du aktivera ATP för hela databasservern och använda det för alla databaser på servern. 

Priser för ATP överensstämmer med Azure Security Center standard-nivån $15/nod/månad, där varje skyddad SQL Database-server räknas som en nod. De första 60 dagarna efter aktivering betraktas som en kostnadsfri utvärderingsversion under och debiteras inte. Mer information finns i den [sidan med priser för Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Komma igång med ATP 
Följande steg hjälper dig igång med ATP. 

## <a name="1-enable-atp"></a>1. Aktivera ATP

Aktivera ATP genom att gå till **Advanced Threat Protection** under den **Security** riktningen i din Azure SQL Database-fönstret. Om du vill aktivera ATP för alla databaser på servern, klickar du på **aktivera Avancerat skydd på servern**.

![Aktivera ATP](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Kostnaden för ATP är $15/nod/månad, där en nod är den hela logiska SQL-servern. Du därför betalar bara en gång för att skydda alla databaser på servern med ATP. De första 60 dagarna betraktas som en kostnadsfri utvärderingsversion.

## <a name="2-configure-vulnerability-assessment"></a>2. Konfigurera utvärdering av säkerhetsrisker

Om du vill börja använda Vulnerability Assessment, måste du konfigurera ett lagringskonto där sökresultat sparas. Om du vill göra det klickar du på kortet utvärdering av säkerhetsrisker.

![Konfigurera VA](./media/sql-advanced-protection/configure_va.png) 

Välj eller skapa ett lagringskonto för att spara sökresultat. Du kan också aktivera periodiskt återkommande sökningar för att konfigurera Sårbarhetsbedömning för att köra automatiska sökningar en gång i veckan. En sammanfattning för resultatet av genomsökningen skickas till de e-postadresser som du anger.

![VA-inställningar](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Börja klassificera data, spåra säkerhetsrisker och undersöka hotaviseringar

Klicka på den **Dataidentifiering och klassificering** kort för att visa rekommenderade känsliga kolumner att klassificera och för att klassificera dina data med beständiga känslighet etiketter. Klicka på den **Sårbarhetsbedömning** kort att visa och hantera sårbarhetsgenomsökningar och rapporter och spåra din säkerhet datasekretesstandarder. Om säkerhetsaviseringar har tagits emot, klickar du på den **Hotidentifiering** kort att visa information om av aviseringarna och för att se en sammanfattande rapport med alla aviseringar i Azure-prenumerationen via sidan för security-aviseringar i Azure Security Center.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Hantera ATP-inställningar på din SQL-server

Om du vill visa och hantera inställningarna för avancerat skydd, gå till **Advanced Threat Protection** under den **Security** riktningen i din SQL server-fönstret. På den här sidan kan du aktivera eller inaktivera ATP och ändra inställningarna för Hotidentifiering för hela SQL-servern.

![Serverinställningar](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. Hantera ATP-inställningar för en SQL-databas

Om du vill åsidosätta inställningarna för en viss databas ATP Hotidentifiering, kontrollera den **aktivera Avancerat Hotskydd på databasnivå** kryssrutan. Använd det här alternativet bara om du har ett särskilt krav att ta emot separata hotidentifieringsaviseringar för enskilda databasen, i stället för eller förutom de aviseringar som togs emot för alla databaser på servern. 

När kryssrutan är markerad, klickar du på **hotidentifieringsinställningarna för den här databasen** och konfigurera relevanta inställningar för den här databasen.

![Identifieringsinställningar i databasen och hot](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Inställningarna för avancerat skydd för servern kan även nås från fönstret ATP-databasen. Klicka på **inställningar** i ATP huvudfönstret och klicka sedan på **vyn Avancerat skydd-serverinställningar**. 

![Databasinställningar](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Nästa steg 

- Läs mer om [Dataidentifiering och klassificering](sql-database-data-discovery-and-classification.md) 
- Läs mer om [utvärdering av säkerhetsrisker](sql-vulnerability-assessment.md) 
- Läs mer om [Hotidentifiering](sql-database-threat-detection.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
