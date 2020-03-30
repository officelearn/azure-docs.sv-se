---
title: Aktivera automatisk inställning
description: Du kan enkelt aktivera automatisk justering på din Azure SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: eed839c277156046ff9b7d97c6e87636a0822889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299336"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Aktivera automatisk justering för att övervaka frågor och förbättra arbetsbelastningens prestanda

Azure SQL Database är en automatiskt hanterad datatjänst som ständigt övervakar dina frågor och identifierar den åtgärd som du kan utföra för att förbättra prestanda för din arbetsbelastning. Du kan granska rekommendationer och manuellt tillämpa dem, eller låta Azure SQL Database automatiskt tillämpa korrigerande åtgärder - det kallas **automatiskt justeringsläge**.

Automatisk justering kan aktiveras på server- eller databasnivå via [Azure-portalen,](sql-database-automatic-tuning-enable.md#azure-portal) [REST API-anrop](sql-database-automatic-tuning-enable.md#rest-api) och [T-SQL-kommandon.](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)

> [!NOTE]
> För hanterad instans kan det alternativ som stöds FORCE_LAST_GOOD_PLAN konfigureras endast via [T-SQL.](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) Portalbaserade konfigurations- och automatiska indexjusteringsalternativ som beskrivs i den här artikeln gäller inte hanterad instans.

> [!NOTE]
> Det går inte att konfigurera automatisk justeringsalternativ via ARM-mallen (Azure Resource Manager) just nu.

## <a name="enable-automatic-tuning-on-server"></a>Aktivera automatisk justering på servern

På servernivå kan du välja att ärva automatisk justeringskonfiguration från "Azure Defaults" eller inte ärva konfigurationen. Azure-standardvärden är FORCE_LAST_GOOD_PLAN är aktiverat, CREATE_INDEX är aktiverat och DROP_INDEX är inaktiverat.

> [!IMPORTANT]
> Från och med mars börjar 2020 ändringar av Azure-standardvärden för automatisk justering gälla på följande sätt:
>
> - Nya Azure-standardvärden kommer att vara FORCE_LAST_GOOD_PLAN = aktiverade, CREATE_INDEX = inaktiverade och DROP_INDEX = inaktiverade.
> - Befintliga servrar utan automatiska justeringsinställningar konfigureras automatiskt för att ÄRVA de nya Azure-standardinställningarna. Detta gäller alla kunder som för närvarande har serverinställningar för automatisk justering i ett odefinierat tillstånd.
> - Nya servrar som skapas konfigureras automatiskt för att ÄRVA de nya Azure-standardvärdena (till skillnad från tidigare när automatisk justeringskonfiguration var i ett odefinierat tillstånd när den nya servern skapades).

### <a name="azure-portal"></a>Azure Portal

Om du vill aktivera automatisk justering på den logiska **servern**i Azure SQL Database navigerar du till servern i Azure-portalen och väljer sedan **Automatisk justering** på menyn.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Observera att **DROP_INDEX** alternativet just nu inte är kompatibelt med program som använder partitionsväxling och indextips och bör inte aktiveras i dessa fall. Det går inte att släppa oanvända index för premium- och affärskritiska tjänstnivåer.
>

Välj de automatiska justeringsalternativ som du vill aktivera och välj **Använd**.

Automatiska justeringsalternativ på en server tillämpas på alla databaser på den här servern. Som standard ärver alla databaser konfigurationen från den överordnade servern, men detta kan åsidosättas och anges för varje databas individuellt.

### <a name="rest-api"></a>REST API

Läs mer om hur du använder REST API för att aktivera Automatisk justering på en server, se [SQL Server Automatisk justering UPPDATERING och HÄMTA HTTP-metoder](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Aktivera automatisk justering i en enskild databas

Med Azure SQL-databasen kan du individuellt ange den automatiska justeringskonfigurationen för varje databas. På databasnivå kan du välja att ärva automatisk justeringskonfiguration från den överordnade servern, "Azure Defaults" eller att inte ärva konfigurationen. Azure Defaults är inställda på FORCE_LAST_GOOD_PLAN är aktiverat, CREATE_INDEX är aktiverat och DROP_INDEX är inaktiverad.

> [!TIP]
> Den allmänna rekommendationen är att hantera den automatiska justeringskonfigurationen på **servernivå** så att samma konfigurationsinställningar kan tillämpas på varje databas automatiskt. Konfigurera automatisk justering på en enskild databas endast om du behöver databasen för att ha andra inställningar än andra som ärver inställningar från samma server.
>

### <a name="azure-portal"></a>Azure Portal

Om du vill aktivera automatisk justering i en **enda databas**navigerar du till databasen i Azure-portalen och väljer **Automatisk justering**.

Individuella automatiska justeringsinställningar kan konfigureras separat för varje databas. Du kan konfigurera ett individuellt alternativ för automatisk justering manuellt eller ange att ett alternativ ärver dess inställningar från servern.

![Databas](./media/sql-database-automatic-tuning-enable/database.png)

Observera att DROP_INDEX alternativet just nu inte är kompatibelt med program som använder partitionsväxling och indextips och bör inte aktiveras i dessa fall.

När du har valt önskad konfiguration klickar du på **Använd**.

### <a name="rest-api"></a>API för vila

Läs mer om hur du använder REST API för att aktivera Automatisk justering i en enda databas, se [SQL Database Automatisk justering UPPDATERING och GET HTTP-metoder](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Om du vill aktivera automatisk justering på en enda databas via T-SQL ansluter du till databasen och kör följande fråga:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Om du ställer in automatisk justering på AUTO tillämpas Azure Defaults. Om du ställer in den på INHERIT kommer automatisk justeringskonfiguration att ärvas från den överordnade servern. Om du väljer ANPASSAD måste du konfigurera automatisk justering manuellt.

Om du vill konfigurera enskilda automatiska justeringsalternativ via T-SQL ansluter du till databasen och kör frågan, till exempel den här:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Om du ställer in det enskilda justeringsalternativet på PÅ åsidosätter du alla inställningar som databasen ärvt och aktiverar justeringsalternativet. Om du ställer in den på OFF åsidosätts även alla inställningar som databasen ärvt och inaktiverar inställningsalternativet. Det automatiska justeringsalternativet, som STANDARD har angetts för, ärver den automatiska justeringskonfigurationen från servernivåinställningarna.  

> [!IMPORTANT]
> Vid [aktiv geo-replikering](sql-database-auto-failover-group.md)måste automatisk justering endast konfigureras på den primära databasen. Automatiskt tillämpade justeringsåtgärder replikeras till exempel indexskapande eller borttagning automatiskt till den skrivskyddade sekundära. Försöker aktivera automatisk justering via T-SQL på den skrivskyddade sekundära kommer att resultera i ett fel som har en annan justering konfiguration på den skrivskyddade sekundära stöds inte.
>

Hitta våra mer abut T-SQL alternativ för att konfigurera Automatisk justering, se [ALTER DATABASE SET Options (Transact-SQL) för SQL Database server](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Inaktiverad av systemet

Automatisk justering övervakar alla åtgärder som krävs på databasen och i vissa fall kan den avgöra att automatisk justering inte kan fungera korrekt i databasen. I det här fallet kommer justeringsalternativet att inaktiveras av systemet. I de flesta fall beror detta på att Query Store inte är aktiverat eller om det är skrivskyddat tillstånd i en viss databas.

## <a name="permissions"></a>Behörigheter

Eftersom automatisk justering är Azure-funktion måste du använda Azures inbyggda RBAC-roller för att kunna använda den. Att bara använda SQL-autentisering räcker inte för att använda funktionen från Azure-portalen.

Om du vill använda automatisk justering är den minsta behörighet som krävs för att bevilja användaren Azures inbyggda [SQL DB-deltagarroll.](../role-based-access-control/built-in-roles.md#sql-db-contributor) Du kan också överväga att använda roller med högre behörighet, till exempel SQL Server Contributor, Contributor och Owner.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurera automatiska inställningsmeddelanden för e-postmeddelanden

Se [guiden för automatisk justering av e-postmeddelanden.](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>Nästa steg

* Läs [artikeln Automatisk justering](sql-database-automatic-tuning.md) om du vill veta mer om automatisk justering och hur det kan hjälpa dig att förbättra dina resultat.
* Se [Prestandarekommendationer](sql-database-advisor.md) för en översikt över prestandarekommendationer för Azure SQL Database.
* Se [Frågeprestandastatistik](sql-database-query-performance.md) om du vill veta mer om hur du visar prestandapåverkan för dina vanligaste frågor.
