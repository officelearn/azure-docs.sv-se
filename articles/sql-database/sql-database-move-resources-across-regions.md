---
title: Så här flyttar du Azure SQL Database resurser till en annan region | Microsoft Docs
description: Lär dig hur du flyttar Azure SQL Database, Azure SQL elastisk pool eller Azure SQL-hanterad instans till en annan region.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 2158d4120445de4c62461fb89555a1b73bc1e2b4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567156"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Flytta Azure SQL-resurser till en annan region

I den här artikeln lär du dig ett allmänt arbets flöde för att flytta Azure SQL Database enskild databas, elastisk pool och hanterad instans till en ny region. 

## <a name="overview"></a>Översikt

Det finns olika scenarier där du vill flytta dina befintliga Azure SQL-resurser från en region till en annan. Du kan till exempel expandera din verksamhet till en ny region och vill optimera den för den nya kund basen. Eller så måste du flytta åtgärderna till en annan region för att kunna följa dessa orsaker. Eller Azure har lanserat en helt ny region som ger bättre närhet och förbättrar kund upplevelsen.  

Den här artikeln innehåller ett allmänt arbets flöde för att flytta resurser till en annan region. Arbets flödet består av följande steg: 

- Kontrol lera förutsättningarna för flytten 
- Förbered för att flytta resurserna i omfånget
- Övervaka förberedelse processen
- Testa flyttnings processen
- Påbörja den faktiska flytten 
- Ta bort resurserna från käll regionen 


> [!NOTE]
> Den här artikeln gäller migreringar i det offentliga Azure-molnet eller inom samma suveräna moln. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Flytta en enskild databas

### <a name="verify-prerequisites"></a>Kontrollera förutsättningar 

1. Skapa en logisk mål server för varje käll Server. 
1. Konfigurera brand väggen med rätt undantag med hjälp av [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Konfigurera de logiska servrarna med korrekta inloggningar. Om du inte är prenumerations administratör eller SQL Server-administratör kan du arbeta med administratören för att tilldela de behörigheter som du behöver. Mer information finns i [Hantera säkerhet för Azure SQL Database efter haveri beredskap](sql-database-geo-replication-security-config.md). 
1. Om dina databaser är krypterade med TDE och använder din egen krypterings nyckel i Azure Key Vault, se till att rätt krypterings material är etablerad i mål regionerna. Mer information finns i [Azure SQL Transparent datakryptering med Kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Om granskning på databas nivå är aktiverat inaktiverar du den och aktiverar granskning på server nivå i stället. Efter redundansväxlingen kräver granskning på databas nivå trafiken över flera regioner, vilket inte är önskvärt eller möjligt efter flytten. 
1. För granskning på server nivå, se till att:
   - Lagrings behållaren, Log Analytics eller händelsehubben med befintliga gransknings loggar flyttas till mål regionen. 
   - Granskning har kon figurer ATS på mål servern. Mer information finns i [Kom igång med SQL Database-granskning](sql-database-auditing.md). 
1. Om instansen har en långsiktig bevarande princip (brv) förblir de befintliga säkerhets kopieringarna fortfarande kopplade till den aktuella servern. Eftersom mål servern är annorlunda kommer du att kunna komma åt äldre LTR-säkerhetskopieringar i käll regionen med käll servern, även om servern tas bort. 

  > [!NOTE]
  > Detta är inte tillräckligt för att flytta mellan det suveräna molnet och en offentlig region. En sådan migrering kräver att säkerhets kopieringen av säkerhets kopian flyttas till mål servern, vilket inte stöds för närvarande. 

### <a name="prepare-resources"></a>Förbereda resurser

1. Skapa en [grupp för redundans](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) mellan den logiska servern för källan och målets logiska Server.  
1. Lägg till de databaser som du vill flytta till gruppen redundans. 
    - Replikeringen av alla tillagda databaser kommer att initieras automatiskt. Mer information finns i [metod tips för att använda failover-grupper med enskilda databaser](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Övervaka förberedelse processen

Du kan regelbundet anropa [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) för att övervaka replikeringen av dina databaser från källan till målet. Objektet utdata i `Get-AzSqlDatabaseFailoverGroup` innehåller en egenskap för **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) anger att databasen är synkroniserad och kan växlas på ett säkert sätt. 
   - **ReplicationState = 0** (SEEDing) visar att databasen inte har dirigerats än och att ett försök att redundansväxla Miss lyckas. 

### <a name="test-synchronization"></a>Testa synkronisering

När **ReplicationState** är `2`det ansluter du till varje databas eller en delmängd av databaser med hjälp `<fog-name>.secondary.database.windows.net` av den sekundära slut punkten och utför en fråga mot databaserna för att säkerställa anslutning, lämplig säkerhets konfiguration och data replikering. 

### <a name="initiate-the-move"></a>Påbörja flytten

1. Anslut till mål servern med hjälp av den sekundära `<fog-name>.secondary.database.windows.net`slut punkten.
1. Använd [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) för att växla den sekundära hanterade instansen som primär med fullständig synkronisering. Den här åtgärden kommer att lyckas eller återställas. 
1. Kontrol lera att kommandot har slutförts genom att `nslook up <fog-name>.secondary.database.windows.net` använda för att kontrol lera att DNS CNAME-posten pekar på mål regionens IP-adress. Om switch-kommandot Miss lyckas kommer CNAME inte att uppdateras. 

### <a name="remove-the-source-databases"></a>Ta bort käll databaserna

När flyttningen är klar tar du bort resurserna i käll regionen för att undvika onödiga kostnader. 

1. Ta bort gruppen redundans med [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Ta bort varje käll databas med [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) för varje databas på käll servern. Detta avslutar Länkar för geo-replikering automatiskt. 
1. Ta bort käll servern med hjälp av [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Ta bort nyckel valvet, granska lagrings behållare, händelsehubben, AAD-instansen och andra beroende resurser för att sluta faktureras för dem. 

## <a name="move-elastic-pools"></a>Flytta elastiska pooler

### <a name="verify-prerequisites"></a>Kontrollera förutsättningar 

1. Skapa en logisk mål server för varje käll Server. 
1. Konfigurera brand väggen med rätt undantag med hjälp av [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Konfigurera de logiska servrarna med korrekta inloggningar. Om du inte är prenumerations administratör eller SQL Server-administratör kan du arbeta med administratören för att tilldela de behörigheter som du behöver. Mer information finns i [Hantera säkerhet för Azure SQL Database efter haveri beredskap](sql-database-geo-replication-security-config.md). 
1. Om dina databaser är krypterade med TDE och använder din egen krypterings nyckel i Azure Key Vault, se till att rätt krypterings material är etablerad i mål regionen.
1. Skapa en elastisk mål grupp för varje elastisk pool och se till att poolen skapas på samma tjänst nivå, med samma namn och samma storlek. 
1. Om en databas nivå granskning är aktive rad inaktiverar du den och aktiverar granskning på server nivå i stället. Efter redundansväxlingen kräver granskning på databas nivå trafik över flera regioner, vilket inte önskas, eller möjligt efter flytten. 
1. För granskning på server nivå, se till att:
    - Lagrings behållaren, Log Analytics eller händelsehubben med befintliga gransknings loggar flyttas till mål regionen.
    - Gransknings konfigurationen konfigureras på mål servern. Mer information finns i [granskning av SQL Database](sql-database-auditing.md).
1. Om instansen har en långsiktig bevarande princip (brv) förblir de befintliga säkerhets kopieringarna fortfarande kopplade till den aktuella servern. Eftersom mål servern är annorlunda kommer du att kunna komma åt äldre LTR-säkerhetskopieringar i käll regionen med käll servern, även om servern tas bort. 

  > [!NOTE]
  > Detta är inte tillräckligt för att flytta mellan det suveräna molnet och en offentlig region. En sådan migrering kräver att säkerhets kopieringen av säkerhets kopian flyttas till mål servern, vilket inte stöds för närvarande. 

### <a name="prepare-to-move"></a>Förbered för att flytta
 
1.  Skapa en separat [failover-grupp](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) mellan varje elastisk pool på den logiska käll servern och dess motsvarande elastiska pool på mål servern. 
1.  Lägg till alla databaser i poolen i gruppen redundans. 
    - Replikering av tillagda databaser initieras automatiskt. Mer information finns i [metod tips för failover-grupper med elastiska pooler](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Även om det är möjligt att skapa en failover-grupp som innehåller flera elastiska pooler, rekommenderar vi starkt att du skapar en separat failover-grupp för varje pool. Om du har ett stort antal databaser över flera elastiska pooler som du behöver flytta kan du köra förberedelse stegen parallellt och sedan starta flytt steget parallellt. Den här processen skalas bättre och tar mindre tid jämfört med att ha flera elastiska pooler i samma failover-grupp. 

### <a name="monitor-the-preparation-process"></a>Övervaka förberedelse processen

Du kan regelbundet anropa [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) för att övervaka replikeringen av dina databaser från källan till målet. Objektet utdata i `Get-AzSqlDatabaseFailoverGroup` innehåller en egenskap för **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) anger att databasen är synkroniserad och kan växlas på ett säkert sätt. 
   - **ReplicationState = 0** (SEEDing) visar att databasen inte har dirigerats än och att ett försök att redundansväxla Miss lyckas. 

### <a name="test-synchronization"></a>Testa synkronisering
 
När **ReplicationState** är `2`det ansluter du till varje databas eller en delmängd av databaser med hjälp `<fog-name>.secondary.database.windows.net` av den sekundära slut punkten och utför en fråga mot databaserna för att säkerställa anslutning, lämplig säkerhets konfiguration och data replikering. 

### <a name="initiate-the-move"></a>Påbörja flytten
 
1. Anslut till mål servern med hjälp av den sekundära `<fog-name>.secondary.database.windows.net`slut punkten.
1. Använd [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) för att växla den sekundära hanterade instansen som primär med fullständig synkronisering. Den här åtgärden kommer att lyckas eller återställas. 
1. Kontrol lera att kommandot har slutförts genom att `nslook up <fog-name>.secondary.database.windows.net` använda för att kontrol lera att DNS CNAME-posten pekar på mål regionens IP-adress. Om switch-kommandot Miss lyckas kommer CNAME inte att uppdateras. 

### <a name="remove-the-source-elastic-pools"></a>Ta bort de elastiska källorna
 
När flyttningen är klar tar du bort resurserna i käll regionen för att undvika onödiga kostnader. 

1. Ta bort gruppen redundans med [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Ta bort varje elastisk källa på käll servern med hjälp av [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Ta bort käll servern med hjälp av [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Ta bort nyckel valvet, granska lagrings behållare, händelsehubben, AAD-instansen och andra beroende resurser för att sluta faktureras för dem. 

## <a name="move-managed-instance"></a>Flytta hanterad instans

### <a name="verify-prerequisites"></a>Kontrollera förutsättningar
 
1. För varje hanterad käll instans skapar du en mål hanterad instans av samma storlek i mål regionen.  
1. Konfigurera nätverket för en hanterad instans. Mer information finns i [nätverks konfiguration](sql-database-howto-managed-instance.md#network-configuration).
1. Konfigurera mål huvud databasen med korrekta inloggningar. Om du inte är prenumerations administratör eller SQL Server-administratör kan du arbeta med administratören för att tilldela de behörigheter som du behöver. 
1. Om databaserna är krypterade med TDE och använder din egen krypterings nyckel i Azure Key Vault kontrollerar du att AKV med identiska krypterings nycklar finns i både käll-och mål regionerna. Mer information finns i [TDE med Kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Om granskning är aktiverat för instansen kontrollerar du att:
    - Lagrings containern eller händelsehubben med befintliga loggar flyttas till mål regionen. 
    - Granskning har kon figurer ATS på mål instansen. Mer information finns i [granskning med hanterad instans](sql-database-managed-instance-auditing.md).
1. Om instansen har en långsiktig bevarande princip (brv) förblir de befintliga säkerhets kopieringarna fortfarande kopplade till den aktuella servern. Eftersom mål servern är annorlunda kommer du att kunna komma åt äldre LTR-säkerhetskopieringar i käll regionen med käll servern, även om servern tas bort. 

  > [!NOTE]
  > Detta är inte tillräckligt för att flytta mellan det suveräna molnet och en offentlig region. En sådan migrering kräver att säkerhets kopieringen av säkerhets kopian flyttas till mål servern, vilket inte stöds för närvarande. 

### <a name="prepare-resources"></a>Förbereda resurser

Skapa en redundans grupp mellan varje käll instans och motsvarande mål instans.
    - Replikeringen av alla databaser på varje instans kommer att initieras automatiskt. Mer information finns i [grupper för automatisk redundans](sql-database-auto-failover-group.md) .

 
### <a name="monitor-the-preparation-process"></a>Övervaka förberedelse processen

Du kan regelbundet anropa [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) för att övervaka replikeringen av dina databaser från källan till målet. Objektet utdata i `Get-AzSqlDatabaseFailoverGroup` innehåller en egenskap för **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) anger att databasen är synkroniserad och kan växlas på ett säkert sätt. 
   - **ReplicationState = 0** (SEEDing) visar att databasen inte har dirigerats än och att ett försök att redundansväxla Miss lyckas. 

### <a name="test-synchronization"></a>Testa synkronisering

När **ReplicationState** är `2`det ansluter du till varje databas eller en delmängd av databaser med hjälp `<fog-name>.secondary.database.windows.net` av den sekundära slut punkten och utför en fråga mot databaserna för att säkerställa anslutning, lämplig säkerhets konfiguration och data replikering. 

### <a name="initiate-the-move"></a>Påbörja flytten 

1. Anslut till mål servern med hjälp av den sekundära `<fog-name>.secondary.database.windows.net`slut punkten.
1. Använd [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) för att växla den sekundära hanterade instansen som primär med fullständig synkronisering. Den här åtgärden kommer att lyckas eller återställas. 
1. Kontrol lera att kommandot har slutförts genom att `nslook up <fog-name>.secondary.database.windows.net` använda för att kontrol lera att DNS CNAME-posten pekar på mål regionens IP-adress. Om switch-kommandot Miss lyckas kommer CNAME inte att uppdateras. 

### <a name="remove-the-source-managed-instances"></a>Ta bort de hanterade käll instanserna
När flyttningen är klar tar du bort resurserna i käll regionen för att undvika onödiga kostnader. 

1. Ta bort gruppen redundans med [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Detta tar bort konfigurationen av redundanskonfiguration och avslutar geo-replikeringslänken mellan de två instanserna. 
1. Ta bort den hanterade käll instansen med hjälp av [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Ta bort eventuella ytterligare resurser i resurs gruppen, till exempel det virtuella klustret, det virtuella nätverket och säkerhets gruppen. 

## <a name="next-steps"></a>Nästa steg 

[Hantera](sql-database-manage-after-migration.md) din Azure SQL Database när den har migrerats. 

