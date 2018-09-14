---
title: Aktivera automatisk inställning för Azure SQL Database | Microsoft Docs
description: Du kan aktivera automatisk justering på din Azure SQL Database enkelt.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: d825ddd75bfc829aa82bab4cede0381dda2d36da
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575318"
---
# <a name="enable-automatic-tuning"></a>Aktivera automatisk inställning

Azure SQL Database är ett automatiskt hanterad tjänst som ständigt övervakar dina frågor och anger vilka åtgärder som du kan utföra för att förbättra prestanda för din arbetsbelastning. Du kan granska rekommendationer och manuellt tillämpa dem eller låta Azure SQL Database automatiskt tillämpa åtgärder – den här funktionen kallas **läget för automatisk justering**.

Automatisk justering kan aktiveras på servern eller databasnivå via den [Azure-portalen](sql-database-automatic-tuning-enable.md#azure-portal), [REST API](sql-database-automatic-tuning-enable.md#rest-api) anrop och [T-SQL](sql-database-automatic-tuning-enable.md#t-sql) kommandon.

## <a name="enable-automatic-tuning-on-server"></a>Aktivera automatisk justering på servern
Du kan välja att ärver automatiska justeringskonfigurationer från ”Azure standard” eller inte ärver konfigurationen på servernivå. Azure-standardinställningar är FORCE_LAST_GOOD_PLAN är aktiverad, CREATE_INDEX är aktiverad och DROP_INDEX är inaktiverad.

### <a name="azure-portal"></a>Azure Portal
Aktivera automatisk justering på Azure SQL Database logiska **server**, gå till servern i Azure-portalen och välj **automatisk justering** på menyn.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Tänk på att **DROP_INDEX** alternativet just nu är inte kompatibel med program som använder partition växla och index-tips och bör inte aktiveras i dessa fall.
>

Välj de automatiska justeringsalternativ som du vill aktivera och välj **tillämpa**.

Alternativen för automatisk justering på en server som tillämpas på alla databaser på den här servern. Som standard alla databaser ärver konfigurationen från sina överordnade servern, men detta kan åsidosättas och anges för varje databas individuellt.

### <a name="rest-api"></a>REST-API

Lär dig mer om hur du använder REST API för att aktivera automatisk justering på en server finns [SQL Server automatisk justering uppdatering och GET HTTP-metoder](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).


## <a name="enable-automatic-tuning-on-an-individual-database"></a>Aktivera automatisk justering på en enskild databas

Azure SQL Database kan du ange konfigurationen för automatisk justering för varje databas individuellt. Du kan välja att ärver automatiska justeringskonfigurationer från den överordnade servern som ”Azure standard” eller inte ärver konfigurationen på databasnivå. Azure-standardinställningar är inställda på att FORCE_LAST_GOOD_PLAN är aktiverad, CREATE_INDEX är aktiverad och DROP_INDEX är inaktiverad.

> [!NOTE]
> Allmän rekommendation är att hantera konfigurationen för automatisk justering på **servernivå** så att samma inställningar kan tillämpas på varje databas automatiskt. Konfigurera automatisk justering på en individuell databas enbart om du behöver att databasen ska ha olika inställningar än andra ärver inställningar från samma server.
>

### <a name="azure-portal"></a>Azure Portal

Aktivera automatisk justering på en **enkel databas**går du till databasen i Azure-portalen och väljer **automatisk justering**.

Enskilda inställningar för automatisk justering kan konfigureras separat för varje databas. Du kan manuellt konfigurera en enskild alternativ för automatisk justering, eller ange att ett alternativ ärver dess inställningar från servern.

![Databas](./media/sql-database-automatic-tuning-enable/database.png)

Observera att alternativet DROP_INDEX just nu är inte kompatibel med program som använder partition växla och index-tips och bör inte aktiveras i dessa fall.

När du har valt önskad konfiguration, klickar du på **tillämpa**.

### <a name="rest-api"></a>REST API

Lär dig mer om hur du använder REST API för att aktivera automatisk justering på en enskild databas, se [SQL Database automatisk justering uppdatering och GET HTTP-metoder](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Om du vill aktivera automatisk justering på en enda databas via T-SQL, ansluta till databasen och kör följande fråga:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Ställa in automatisk justering Auto gäller Azure som standard. Ange värdet till ÄRV, ärvs konfigurationen för automatisk justering från den överordnade servern. Välja anpassade behöver du du manuellt konfigurera automatisk justering.

Om du vill konfigurera enskilda alternativen för automatisk justering via T-SQL, ansluta till databasen och köra frågor som följande:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Ställa in ett justeringsalternativ för enskilda på, åsidosätter inställningar som databasen ärvt och aktivera ett justeringsalternativ. Ange värdet till OFF, kommer också åsidosätter inställningar som databasen ärvt och inaktiverar alternativet justering. Alternativ för automatisk justering, som standard anges ärver konfigurationen från automatisk justering inställningen databasnivå.  

Hitta mer om datorns T-SQL-alternativ för att konfigurera automatisk justering, se [ALTER DATABASE SET-alternativ (Transact-SQL) för SQL Database-logisk server](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=sql-server-2017&tabs=sqldbls#arguments-1).

## <a name="disabled-by-the-system"></a>Inaktiverad av systemet
Automatisk justering övervakar alla de åtgärder som utförs på databasen och i vissa fall kan bestämma att automatisk justering inte kan fungerar korrekt på databasen. I det här fallet inaktiveras inställning av alternativet av systemet. I de flesta fall inträffar detta eftersom Query Store inte är aktiverat eller är i skrivskyddat läge på en viss databas.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurera automatisk justering e-postaviseringar

Se [automatisk justering e-postaviseringar](sql-database-automatic-tuning-email-notifications.md) guide.

## <a name="next-steps"></a>Nästa steg
* Läs den [automatisk justering artikeln](sql-database-automatic-tuning.md) mer information om automatisk justering och hur det kan hjälpa dig att förbättra prestanda.
* Se [prestandarekommendationer](sql-database-advisor.md) en översikt över Azure SQL Database prestandarekommendationer.
* Se [fråga Prestandainsikter](sql-database-query-performance.md) att lära dig om hur du visar prestandapåverkan från återställning av dina viktigaste frågor.
