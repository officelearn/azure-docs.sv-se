---
title: Advanced Threat Protection - Azure SQL Database | Microsoft Docs
description: Lär dig mer om funktioner för att identifiera och klassificera känsliga data, hantera din databas säkerhetsrisker och identifiering av avvikande aktiviteter som kan innebära ett hot mot din Azure SQL-databas.
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 5/17/2018
ms.author: ronitr
ms.reviewer: carlrab
ms.openlocfilehash: da21a0b66d86b4cc3e2dc59bdd972d4e24d7e5ec
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34305510"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection för Azure SQL-databas

SQL avancerade Threat Protection är en enhetlig paket för avancerade SQL-säkerhetsfunktioner. Den innehåller funktioner för att identifiera och klassificera känsliga data, hantera din databas säkerhetsrisker och identifiering av avvikande aktiviteter som kan innebära ett hot mot din databas. Det ger en enda gå-till-plats för att aktivera och hantera dessa funktioner. 

## <a name="overview"></a>Översikt

SQL avancerade Threat Protection (ATP) innehåller en uppsättning avancerade SQL-säkerhetsfunktioner, inklusive identifiering av Data och klassificering, Vulnerability Assessment och Hotidentifiering. 

- [Identifiering av data & klassificering](sql-database-data-discovery-and-classification.md) (för närvarande i förhandsversion) ger funktioner finns inbyggda i Azure SQL Database för identifiering, klassificering, etiketter och skydda känsliga data i databasen. Det kan användas för att ge insyn i din databas klassificering tillstånd och att spåra åtkomst till känsliga data i databasen och utanför.
- [Utvärdering av säkerhetsrisker](sql-vulnerability-assessment.md) är ett enkelt att konfigurera tjänsten som kan identifiera, spåra och hjälpa dig att åtgärda eventuella säkerhetsrisker i databasen. Den ger inblick i dina säkerhetstillstånd och inkluderar tillämplig steg för att lösa säkerhetsproblem och förbättra din databas fortifications.
- [Hotidentifiering](sql-database-threat-detection.md) identifierar avvikande aktiviteter som anger ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din databas. Den kontinuerligt övervakar databasen för misstänkta aktiviteter och ger omedelbar säkerhetsaviseringar på potentiella säkerhetsproblem och SQL injection attacker avvikande databasen åtkomstmönster. Hotidentifieringsaviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärd att undersöka och minska risken.

Aktivera SQL ATP när ingår för att aktivera alla dessa funktioner. Med ett enda klick kan du aktivera ATP på databasservern hela gäller för alla databaser på servern. 

Priser för ATP justerar med Azure Security Center standardnivån $15/nod/månad, där varje skyddade SQL Database-servern räknas som en nod. De första 60 dagarna efter aktivering betraktas som en gratis och debiteras inte. Mer information finns i [sida med priser för Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Komma igång med ATP 
Följande steg komma igång med ATP. 

## <a name="1-enable-atp"></a>1. Aktivera ATP

Aktivera ATP genom att gå till **Advanced Threat Protection** under den **säkerhet** rubriken i Azure SQL Database-fönstret. Om du vill aktivera ATP för alla databaser på servern, klickar du på **aktivera Advanced Threat Protection på servern**.

![Aktivera ATP](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> Kostnaden för ATP är $15/nod/månad, där en nod är hela logiska SQL-servern. Du därför betalar bara en gång för att skydda alla databaser på servern med ATP. De första 60 dagarna betraktas som en kostnadsfri utvärderingsversion.

## <a name="2-configure-vulnerability-assessment"></a>2. Konfigurera utvärdering av säkerhetsrisker

Om du vill börja använda Vulnerability Assessment, måste du konfigurera ett lagringskonto där sökresultat sparas. Om du vill göra det klickar du på kortet Vulnerability Assessment.

![Konfigurera VA](./media/sql-advanced-protection/configure_va.png) 

Välj eller skapa ett lagringskonto för att spara sökresultat. Du kan också aktivera regelbundet återkommande genomsökningar konfigurera Vulnerability Assessment om du vill köra automatiska sökningar en gång per vecka. En sökning resultatet sammanfattning skickas till den e-adress som du anger.

![VA inställningar](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Starta klassificera data, spårning säkerhetsrisker och undersöka aviseringar för hot

Klicka på den **Data identifiering och klassificering** kort för att visa rekommenderade känsliga kolumner klassificera och klassificera dina data med beständiga känslighet etiketter. Klicka på den **Vulnerability Assessment** kort för att visa och hantera sårbarhetsgenomsökningar och rapporter och spåra din säkerhet status. Om säkerhetsaviseringar har tagits emot, klickar du på den **Hotidentifiering** kort för att visa detaljer för aviseringar och visa en sammanställning på alla aviseringar i Azure-prenumerationen via sidan Azure Security Center säkerhet aviseringar.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Hantera inställningar för ATP på SQLServer

Om du vill visa och hantera Advanced Threat Protection-inställningar, gå till **Advanced Threat Protection** under den **säkerhet** rubriken i din SQL server-fönstret. På den här sidan kan du aktivera eller inaktivera ATP och ändra Hotidentifiering inställningarna för hela SQLServer.

![Serverinställningar](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. Hantera ATP-inställningarna för en SQL-databas

Om du vill åsidosätta ATP Hotidentifiering inställningar för en viss databas, kontrollera den **aktivera Advanced Threat Protection på databasnivå** kryssrutan. Använd det här alternativet om du har ett visst krav för att ta emot separat hotidentifieringsaviseringar för enskilda-databasen i stället för eller förutom aviseringar för alla databaser på servern. 

När kryssrutan är markerad klickar du på **Hotidentifiering inställningar för den här databasen** och konfigurera relevanta inställningar för den här databasen.

![Databasen och hot identifieringsinställningar](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Avancerade Threat Protection-inställningar för servern kan även nås från fönstret ATP-databasen. Klicka på **inställningar** i huvudfönstret ATP och klicka sedan på **visa Advanced Threat Protection-serverinställningar**. 

![Databasinställningar](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Nästa steg 

- Lär dig mer om [Data Discovery & klassificering](sql-database-data-discovery-and-classification.md) 
- Lär dig mer om [utvärdering av säkerhetsrisker](sql-vulnerability-assessment.md) 
- Lär dig mer om [Hotidentifiering](sql-database-threat-detection.md)
- Lär dig mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
