---
title: Aktivera automatisk justering för Azure SQL Database | Microsoft Docs
description: Du kan aktivera automatisk justering på Azure SQL Database enkelt.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 457ee34daf368150a8703ea32a39b2350d654523
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569417"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Aktivera automatisk justering för att övervaka frågor och förbättra arbets Belastningens prestanda

Azure SQL Database är en automatiskt hanterad data tjänst som kontinuerligt övervakar dina frågor och identifierar den åtgärd som du kan utföra för att förbättra arbets Belastningens prestanda. Du kan granska rekommendationer och manuellt tillämpa dem, eller låta Azure SQL Database automatiskt tillämpa korrigerande åtgärder – detta kallas **automatiskt justerings läge**.

Automatisk justering kan aktive ras på servern eller databas nivån via [Azure Portal](sql-database-automatic-tuning-enable.md#azure-portal), [REST API](sql-database-automatic-tuning-enable.md#rest-api) anrop och [T-SQL-](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) kommandon.

> [!NOTE]
> För en hanterad instans kan alternativet FORCE_LAST_GOOD_PLAN endast konfigureras via [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . Portalbaserade konfigurations alternativ och automatiska index justeringar som beskrivs i den här artikeln gäller inte för hanterade instanser.

> [!NOTE]
> Det går inte att konfigurera alternativ för automatisk justering via ARM-mallen (Azure Resource Manager) för tillfället.

## <a name="enable-automatic-tuning-on-server"></a>Aktivera automatisk justering på servern

På server nivå kan du välja att ärva konfigurationen för automatisk justering från "Azure-standardvärden" eller att inte ärva konfigurationen. Azure-standardvärden är FORCE_LAST_GOOD_PLAN, CREATE_INDEX är aktiverat och DROP_INDEX är inaktive rad.

### <a name="azure-portal"></a>Azure Portal

Om du vill aktivera automatisk justering på Azure SQL Database logisk **Server**navigerar du till servern i Azure Portal och väljer sedan **Automatisk justering** i menyn.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Observera att alternativet **DROP_INDEX** för tillfället inte är kompatibelt med program som använder partitions växlings-och index tips och inte bör aktive ras i dessa fall.
>

Välj de automatiska justerings alternativ som du vill aktivera och välj **Använd**.

Automatiska justerings alternativ på en server tillämpas på alla databaser på den här servern. Som standard ärver alla databaser konfigurationen från sin överordnade Server, men detta kan åsidosättas och anges för varje databas individuellt.

### <a name="rest-api"></a>REST-API

Ta reda på mer om hur du använder REST API för automatisk justering på en server, se [SQL Server automatisk justering uppdatering och hämta HTTP-metoder](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Aktivera automatisk justering på en enskild databas

Med Azure SQL Database kan du individuellt ange konfigurationen för automatisk justering för varje databas. På databas nivå kan du välja att ärva konfigurationen för automatisk justering från den överordnade servern, "Azure-standardvärden" eller att inte ärva konfigurationen. Standardinställningarna för Azure har angetts till FORCE_LAST_GOOD_PLAN är aktive rad, CREATE_INDEX är aktive rad och DROP_INDEX är inaktive rad.

> [!TIP]
> Den allmänna rekommendationen är att hantera konfigurationen för automatisk justering på **Server nivå** så att samma konfigurations inställningar kan tillämpas automatiskt på alla databaser. Konfigurera automatisk justering endast på en enskild databas om du behöver att databasen har andra inställningar än andra som ärver inställningar från samma server.
>

### <a name="azure-portal"></a>Azure Portal

Om du vill aktivera automatisk justering på en **enskild databas**går du till databasen i Azure Portal och väljer **Automatisk justering**.

Enskilda inställningar för automatisk justering kan konfigureras separat för varje databas. Du kan konfigurera ett enskilt alternativ för automatisk justering manuellt eller ange att ett alternativ ärver inställningarna från servern.

![Databas](./media/sql-database-automatic-tuning-enable/database.png)

Observera att alternativet DROP_INDEX för tillfället inte är kompatibelt med program som använder partitions växlings-och index tips och inte bör aktive ras i dessa fall.

När du har valt önskad konfiguration klickar du på **Använd**.

### <a name="rest-api"></a>REST-API

Lär dig mer om hur du använder REST API för automatisk justering i en enskild databas finns i [SQL Database automatisk uppdatering av uppdatering och hämta HTTP-metoder](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Om du vill aktivera automatisk justering på en enskild databas via T-SQL ansluter du till databasen och kör följande fråga:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Om du ställer in automatisk justering på AUTO används standardinställningarna för Azure. Om du anger att den ska ÄRVAs, kommer den automatiska justerings konfigurationen att ärvas från den överordnade servern. Om du väljer Anpassad måste du konfigurera automatisk justering manuellt.

Om du vill konfigurera enskilda alternativ för automatisk justering via T-SQL ansluter du till databasen och kör frågan, till exempel den här:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Om du ställer in alternativet för enskild justering till på, åsidosätter alla inställningar som databasen har ärvt och aktiverar justerings alternativet. Att ställa in det på av, åsidosätter även alla inställningar som databasen har ärvt och inaktiverar justerings alternativet. Alternativet för automatisk justering, för vilka DEFAULT anges, kommer att ärva konfigurationen från inställningen för automatisk justering i databas nivån.  

> [!IMPORTANT]
> I händelse av [aktiv geo-replikering](sql-database-auto-failover-group.md)behöver automatisk justering bara konfigureras på den primära databasen. Automatiskt tillämpade justerings åtgärder, t. ex. när indexet Create eller Delete replikeras automatiskt till den skrivskyddade sekundära. Om du försöker aktivera automatisk justering via T-SQL på den skrivskyddade sekundära sekundären, resulterar det i ett haveri att en annan inställnings konfiguration på den skrivskyddade sekundären inte stöds.
>

Hitta våra fler om verifieringen T-SQL-alternativ för att konfigurera automatisk justering, se [Alter Database set Options (Transact-SQL) för SQL Database Server](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Inaktiverat av systemet

Automatisk justering övervakar alla åtgärder som det tar i databasen och i vissa fall kan det ta reda på att automatisk justering inte fungerar korrekt i databasen. I den här situationen inaktive ras justerings alternativet av systemet. I de flesta fall beror detta på att Frågearkivet inte är aktiverat eller att det är i skrivskyddat läge för en speciell databas.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Konfigurera e-postmeddelanden för automatisk justering

Se guide för [Automatisk justering av e-](sql-database-automatic-tuning-email-notifications.md) postaviseringar.

## <a name="next-steps"></a>Nästa steg

* Läs [artikeln om automatisk justering](sql-database-automatic-tuning.md) för att lära dig mer om automatisk justering och hur det kan hjälpa dig att förbättra prestandan.
* Se [prestanda rekommendationer](sql-database-advisor.md) för en översikt över Azure SQL Database prestanda rekommendationer.
* Mer information om hur du visar prestanda påverkan för dina vanligaste frågor finns i [fråga prestanda](sql-database-query-performance.md) insikter.
