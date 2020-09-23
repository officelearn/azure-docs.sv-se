---
title: Azure Defender för SQL
description: Lär dig mer om funktioner för att hantera dina databas sårbarheter och identifiera avvikande aktiviteter som kan tyda på ett hot mot databasen i Azure SQL Database, Azure SQL-hanterad instans eller Azure-Synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: b789cd423d2260ce709a02ffb3ac5ea500997609
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941364"
---
# <a name="azure-defender-for-sql"></a>Azure Defender för SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Azure Defender för SQL är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Azure Defender är tillgängligt för Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics. Det innefattar funktioner för att identifiera och klassificera känsliga data, lyfta fram och åtgärda potentiella säkerhetsrisker i databasen och identifiera avvikande aktiviteter som kan indikera ett hot mot databasen. Det ger en samlad plats för aktivering och hantering av dessa funktioner.

## <a name="overview"></a>Översikt

Azure Defender innehåller en uppsättning avancerade SQL-säkerhetsfunktioner, inklusive SQL sårbarhets bedömning och Avancerat skydd.
- [Sårbarhets bedömning](sql-vulnerability-assessment.md) är en tjänst som är enkel att konfigurera som kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. Den ger insyn i ditt säkerhets tillstånd och innehåller åtgärder som kan vidtas för att lösa säkerhets problem och förbättra din databas Fortifications.
- [Advanced Threat Protection](threat-detection-overview.md) identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din databas. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhets aviseringar om potentiella sårbarheter, Azure SQL-injektering och avvikande databas åtkomst mönster. Advanced Threat Protection-aviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minska risken.

Aktivera Azure Defender för SQL en gång för att aktivera alla dessa inkluderade funktioner. Med ett klick kan du aktivera Azure Defender för alla databaser på [servern](logical-servers.md) i Azure eller i din SQL-hanterade instans. Aktivering eller hantering av Azure Defender-inställningar kräver att du tillhöra rollen [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) eller någon av databas-eller Server administratörs rollerna.

Mer information om priser för Azure Defender för SQL finns på [sidan med Azure Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-azure-defender"></a>Komma igång med Azure Defender

Följande steg hjälper dig att komma igång med Azure Defender.

## <a name="1-enable-azure-defender"></a>1. Aktivera Azure Defender

Azure Defender kan nås via [Azure Portal](https://portal.azure.com). Aktivera Azure Defender genom att gå till **Security Center** under **säkerhets** rubriken för servern eller den hanterade instansen.

> [!NOTE]
> Ett lagrings konto skapas och konfigureras automatiskt för att lagra dina genomsöknings resultat för **sårbarhets bedömning** . Om du redan har aktiverat Azure Defender för en annan server i samma resurs grupp och region används det befintliga lagrings kontot.
>
> Kostnaden för Azure Defender är anpassad till Azure Security Center standard pris per nod, där en nod är hela servern eller en hanterad instans. Du betalar därför bara en gång för att skydda alla databaser på servern eller den hanterade instansen med Azure Defender. Du kan prova Azure Defender ut från början med en kostnads fri utvärderings version.

## <a name="2-start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. börja spåra sårbarheter och undersöka hot aviseringar

Klicka på kortet för **sårbarhets bedömning** för att visa och hantera sårbarhets genomsökningar och rapporter och för att spåra din säkerhets datasekretesstandarder. Om säkerhets aviseringar har mottagits klickar du på kortet **Avancerat skydd** för att visa information om aviseringarna och visa en konsol IDE rad rapport om alla aviseringar i din Azure-prenumeration via sidan Azure Security Center säkerhets aviseringar.

## <a name="3-manage-azure-defender-settings"></a>3. Hantera Azure Defender-inställningar

Om du vill visa och hantera Azure Defender-inställningar går du till **Security Center** under **säkerhets** rubriken för servern eller den hanterade instansen. På den här sidan kan du aktivera eller inaktivera Azure Defender och ändra inställningarna för sårbarhets bedömning och Avancerat skydd för hela servern eller den hanterade instansen.

## <a name="4-manage-azure-defender-settings-for-a-database"></a>4. Hantera Azure Defender-inställningar för en databas

Om du vill åsidosätta Azure Defender-inställningarna för en viss databas markerar du kryss rutan **Aktivera Azure Defender för SQL på databas nivå** . Använd bara det här alternativet om du har ett särskilt krav för att ta emot separata varningar om Avancerat skydd eller sårbarhets bedömning för den enskilda databasen, i stället för de aviseringar och resultat som tagits emot för alla databaser på servern eller den hanterade instansen.

När kryss rutan är markerad kan du konfigurera de relevanta inställningarna för den här databasen.

Azure Defender för SQL-inställningar för servern eller den hanterade instansen kan också nås från fönstret Azure Defender Database. Klicka på **Inställningar** i huvud fönstret i Azure Defender och klicka sedan på **Visa Azure Defender för SQL Server-inställningar**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [sårbarhets bedömning](sql-vulnerability-assessment.md)
- Läs mer om [Avancerat skydd](threat-detection-configure.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
