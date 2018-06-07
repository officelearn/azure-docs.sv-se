---
title: Aktivera automatisk justering för Azure SQL Database | Microsoft Docs
description: Du kan aktivera automatisk justering på Azure SQL Database enkelt.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: vvasic
ms.openlocfilehash: d4d3b7f54c7393b57339ea149e8a79f97891dc20
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646039"
---
# <a name="enable-automatic-tuning"></a>Aktivera automatisk inställning

Azure SQL Database är en automatiskt hanterade datatjänst som ständigt övervakar dina frågor och anger vilka åtgärder som du kan utföra för att förbättra prestandan för din arbetsbelastning. Du kan granska rekommendationer och manuellt koppla dem, eller låta Azure SQL Database automatiskt tillämpa korrigerande åtgärder – den här funktionen kallas **automatiskt prestandajustering läge**. Automatisk justering kan aktiveras på servern eller databasnivå.

## <a name="enable-automatic-tuning-on-server"></a>Aktivera automatisk justering på servern
Du kan välja att ärva automatisk justering konfiguration från ”Azure standard” eller inte ärva konfigurationen på servernivå. Azure standardvärdena är FORCE_LAST_GOOD_PLAN är aktiverad, CREATE_INDEX är aktiverad och DROP_INDEX är inaktiverad.

### <a name="azure-portal"></a>Azure Portal
Aktivera automatisk justering på Azure SQL Database-logisk **server**, navigera till servern i Azure-portalen och väljer sedan **automatisk justering** på menyn.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Observera att **DROP_INDEX** alternativet just nu är inte kompatibel med program med hjälp av partition växlar och index-tips och inte är aktiverat i dessa fall.
>

Välj Automatisk justering alternativ som du vill aktivera och välja **tillämpa**.

Alternativ för automatisk justering på en server som tillämpas på alla databaser på servern. Som standard alla databaser Ärv konfiguration från sina överordnade servern, men detta kan åsidosättas och anges separat för varje databas.

### <a name="rest-api"></a>REST-API
[Klicka här om du vill läsa mer om hur du aktiverar automatisk justering på servernivå via REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Aktivera automatisk inställning på en individuell databas

Azure SQL-databasen kan du manuellt ange automatisk justering konfigurationen för varje databas. Du kan välja att ärva automatisk justering konfiguration från den överordnade servern, ”Azure standard” eller inte ärva konfigurationen på databasnivå. Azure standardvärden till FORCE_LAST_GOOD_PLAN är aktiverad, CREATE_INDEX är aktiverad och DROP_INDEX är inaktiverad.

> [!NOTE]
> Allmänt rekommenderar vi att hantera automatisk justering konfigurationen på **servernivå** så samma inställningar kan tillämpas på varje databas automatiskt. Konfigurera automatisk inställning på en individuell databas bara om du vill att databasen ska ha andra inställningar än andra ärva inställningar från samma server.
>

### <a name="azure-portal"></a>Azure Portal

Aktivera automatisk inställning på en **enskild databas**går du till databasen i Azure-portalen och väljer **automatisk justering**.

Enskilda inställningar för automatisk justering kan konfigureras separat för varje databas. Du kan manuellt konfigurera en enskild automatisk justering alternativet eller ange att ett alternativ ärver inställningarna från servern.

![Databas](./media/sql-database-automatic-tuning-enable/database.png)

Observera att alternativet DROP_INDEX just nu är inte kompatibel med program med hjälp av partition växlar och index-tips och inte är aktiverat i dessa fall.

När du har valt önskade konfigurationen, klickar du på **tillämpa**.

### <a name="rest-api"></a>REST API
[Klicka här om du vill läsa mer om hur du aktiverar automatisk inställning på en enskild databas via REST API](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

Om du vill aktivera automatisk inställning på en enskild databas via T-SQL, ansluta till databasen och kör följande fråga:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Ställa in automatisk justering Auto gäller Azure som standard. Ange värdet till ÄRV, ärvs automatisk justering konfiguration från den överordnade servern. Välja anpassade, behöver du manuellt konfigurera automatisk justering.

För att konfigurera enskilda alternativ för automatisk justering via T-SQL, ansluta till databasen och köra frågan som den här:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Ange alternativet för enskilda prestandajustering on, åsidosätter inställningar som databasen ärvt och aktivera alternativet justering. Ange värdet till OFF, kommer också åsidosätter inställningar som databasen ärvt och inaktivera alternativet justering. Automatisk justering alternativet som standard anges ärver konfigurationen från automatisk justera inställningen databasnivå.  

## <a name="disabled-by-the-system"></a>Är inaktiverad av systemet
Automatisk justering övervakar de åtgärder som utförs på databasen och i vissa fall kan bestämma att automatisk justering inte fungerar korrekt på databasen. I den här situationen inaktiveras inställningsalternativ av systemet. I de flesta fall inträffar detta eftersom Query Store inte har aktiverats eller så är i skrivskyddat läge på en viss databas.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurera automatisk justering e-postmeddelanden

Se [automatisk justering e-postaviseringar](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>Nästa steg
* Läs den [automatisk justering artikel](sql-database-automatic-tuning.md) lära dig mer om automatisk justering och hur det kan hjälpa dig att förbättra prestandan.
* Se [rekommendationer](sql-database-advisor.md) en översikt över Azure SQL Database prestanda rekommendationer.
* Se [insikter i frågeprestanda](sql-database-query-performance.md) mer information om hur du visar påverkan på prestandan för din de vanligaste frågorna.
