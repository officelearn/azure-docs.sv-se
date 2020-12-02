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
ms.date: 12/01/2020
ms.openlocfilehash: 4bb7f2fd5823a9d8ebf4234f3bb41d955574b838
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451504"
---
# <a name="azure-defender-for-sql"></a>Azure Defender för SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Azure Defender för SQL är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Azure Defender är tillgängligt för Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics. Det innefattar funktioner för att identifiera och klassificera känsliga data, lyfta fram och åtgärda potentiella säkerhetsrisker i databasen och identifiera avvikande aktiviteter som kan indikera ett hot mot databasen. Det ger en samlad plats för aktivering och hantering av dessa funktioner.

## <a name="overview"></a>Översikt

Azure Defender innehåller en uppsättning avancerade SQL-säkerhetsfunktioner, inklusive SQL sårbarhets bedömning och Avancerat skydd.
- [Sårbarhets bedömning](sql-vulnerability-assessment.md) är en tjänst som är enkel att konfigurera som kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. Den ger insyn i ditt säkerhets tillstånd och innehåller åtgärder som kan vidtas för att lösa säkerhets problem och förbättra din databas Fortifications.
- [Advanced Threat Protection](threat-detection-overview.md) identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din databas. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger omedelbara säkerhets aviseringar om potentiella sårbarheter, Azure SQL-injektering och avvikande databas åtkomst mönster. Advanced Threat Protection-aviseringar ger detaljerad information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minska risken.

Du kan aktivera alla de här funktionerna genom att aktivera Azure Defender for SQL en enda gång. Med ett klick kan du aktivera Azure Defender för alla databaser på [servern](logical-servers.md) i Azure eller i din SQL-hanterade instans. Aktivering eller hantering av Azure Defender-inställningar kräver att du tillhöra rollen [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) eller någon av databas-eller Server administratörs rollerna.

Mer information om priser för Azure Defender för SQL finns på [sidan med Azure Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-azure-defender"></a>Komma igång med Azure Defender

Följande steg hjälper dig att komma igång med Azure Defender.

## <a name="enable-azure-defender"></a>Aktivera Azure Defender

Azure Defender kan nås via [Azure Portal](https://portal.azure.com). Aktivera Azure Defender genom att navigera till **Security Center** under **säkerhets** rubriken för servern eller den hanterade instansen.

> [!NOTE]
> Ett lagrings konto skapas och konfigureras automatiskt för att lagra dina genomsöknings resultat för **sårbarhets bedömning** . Om du redan har aktiverat Azure Defender för en annan server i samma resurs grupp och region används det befintliga lagrings kontot.
>
> Kostnaden för Azure Defender är anpassad till Azure Security Center standard pris per nod, där en nod är hela servern eller en hanterad instans. Du betalar därför bara en gång för att skydda alla databaser på servern eller den hanterade instansen med Azure Defender. Du kan prova Azure Defender ut från början med en kostnads fri utvärderings version.

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Aktivera Azure Defender":::

## <a name="start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>Börja spåra sårbarheter och undersöka hot varningar

Klicka på kortet för **sårbarhets bedömning** för att visa och hantera sårbarhets genomsökningar och rapporter och för att spåra din säkerhets datasekretesstandarder. Om säkerhets aviseringar har mottagits klickar du på kortet **Avancerat skydd** för att visa information om aviseringarna och visa en konsol IDE rad rapport om alla aviseringar i din Azure-prenumeration via sidan Azure Security Center säkerhets aviseringar.

## <a name="manage-azure-defender-settings"></a>Hantera Azure Defender-inställningar

Om du vill visa och hantera Azure Defender-inställningar navigerar du till **Security Center** under **säkerhets** rubriken för servern eller den hanterade instansen. På den här sidan kan du aktivera eller inaktivera Azure Defender och ändra inställningarna för sårbarhets bedömning och Avancerat skydd för hela servern eller den hanterade instansen.

:::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="säkerhets Server inställningar":::

## <a name="manage-azure-defender-settings-for-a-database"></a>Hantera Azure Defender-inställningar för en databas

Om du vill åsidosätta Azure Defender-inställningarna för en viss databas markerar du kryss rutan **Aktivera Azure Defender för SQL på databas nivå** i databasens **Security Center** inställningar. Använd bara det här alternativet om du har ett särskilt krav för att ta emot separata varningar om Avancerat skydd eller sårbarhets bedömning för den enskilda databasen, i stället för de aviseringar och resultat som tagits emot för alla databaser på servern eller den hanterade instansen.

När kryss rutan är markerad kan du konfigurera de relevanta inställningarna för den här databasen.

:::image type="content" source="media/azure-defender-for-sql/enable-for-database-level.png" alt-text="Aktivera Azure Defender på databas nivå":::

Azure Defender för SQL-inställningar för servern eller den hanterade instansen kan också nås från fönstret Azure Defender Database. Klicka på **Inställningar** i huvud Security Centers fönstret och klicka sedan på **Visa Azure Defender för SQL Server-inställningar**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [sårbarhets bedömning](sql-vulnerability-assessment.md)
- Läs mer om [Avancerat skydd](threat-detection-configure.md)
- Läs mer om [Azure Security Center](../../security-center/security-center-introduction.md)