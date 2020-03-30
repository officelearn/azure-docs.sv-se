---
title: Så här flyttar du resurser till en annan region
description: Lär dig hur du flyttar din Azure SQL Database, Azure SQL elastisk pool eller Azure SQL-hanterad instans till en annan region.
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
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821441"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Så här flyttar du Azure SQL-resurser till en annan region

I den här artikeln får du ett allmänt arbetsflöde för hur du flyttar en enda Azure SQL Database-databas, elastisk pool och hanterad instans till en ny region. 

## <a name="overview"></a>Översikt

Det finns olika scenarier där du vill flytta dina befintliga Azure SQL-resurser från en region till en annan. Du kan till exempel expandera verksamheten till en ny region och vill optimera den för den nya kundbasen. Eller så måste du flytta åtgärderna till en annan region av efterlevnadsskäl. Eller Så har Azure släppt en helt ny region som ger en bättre närhet och förbättrar kundupplevelsen.  

Den här artikeln innehåller ett allmänt arbetsflöde för att flytta resurser till en annan region. Arbetsflödet består av följande steg: 

- Verifiera förutsättningarna för flytten 
- Förbered att flytta resurserna i omfånget
- Övervaka förberedelseprocessen
- Testa flyttprocessen
- Initiera den faktiska flytten 
- Ta bort resurserna från källregionen 


> [!NOTE]
> Den här artikeln gäller migreringar i det offentliga Azure-molnet eller inom samma suveräna moln. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Flytta en enda databas

### <a name="verify-prerequisites"></a>Kontrollera förutsättningar 

1. Skapa en mål logisk server för varje källserver. 
1. Konfigurera brandväggen med rätt undantag med [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Konfigurera de logiska servrarna med rätt inloggningar. Om du inte är prenumerationsadministratör eller SQL-serveradministratör kan du arbeta tillsammans med administratören för att tilldela de behörigheter du behöver. Mer information finns i [Så här hanterar du Azure SQL-databassäkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md). 
1. Om dina databaser krypteras med TDE och använder din egen krypteringsnyckel i Azure-nyckelvalvet, se till att rätt krypteringsmaterial etableras i målregionerna. Mer information finns i [Azure SQL Transparent Data Encryption med kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Om granskning på databasnivå är aktiverat inaktiverar du den och aktiverar granskning på servernivå i stället. Efter redundans kräver granskning på databasnivå trafik över flera regioner, vilket inte önskas eller är möjligt efter flytten. 
1. För granskningar på servernivå, se till att:
   - Lagringsbehållaren, Logganalysen eller händelsehubben med befintliga granskningsloggar flyttas till målregionen. 
   - Granskningen är konfigurerad på målservern. Mer information finns i [Komma igång med GRANSKNING AV SQL-databas](sql-database-auditing.md). 
1. Om din instans har en långsiktig bevarandeprincip (LTR) förblir de befintliga LTR-säkerhetskopiorna associerade med den aktuella servern. Eftersom målservern är annorlunda kan du komma åt de äldre LTR-säkerhetskopiorna i källregionen med hjälp av källservern, även om servern tas bort. 

  > [!NOTE]
  > Detta kommer inte att vara tillräckligt för att flytta mellan det suveräna molnet och en offentlig region. En sådan migrering kräver att LTR-säkerhetskopiorna flyttas till målservern, vilket för närvarande inte stöds. 

### <a name="prepare-resources"></a>Förbereda resurser

1. Skapa en [redundansk grupp](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) mellan den logiska servern för källan till den logiska servern för målet.  
1. Lägg till de databaser som du vill flytta till redundansgruppen. 
    - Replikering av alla tillagda databaser initieras automatiskt. Mer information finns i [Metodtips för hur du använder redundanskörer med enskilda databaser](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Övervaka förberedelseprocessen

Du kan med jämna mellanrum ringa [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) för att övervaka replikering av dina databaser från källan till målet. Utdataobjektet `Get-AzSqlDatabaseFailoverGroup` innehåller en egenskap för **ReplicationState:** 
   - **ReplicationState = 2** (CATCH_UP) anger att databasen är synkroniserad och kan säkert skickas över. 
   - **ReplicationState = 0** (SEEDNING) anger att databasen ännu inte är sådd och ett försök att redundans misslyckas. 

### <a name="test-synchronization"></a>Testsynkronisering

När **ReplicationState** är `2`, ansluta till varje databas, eller delmängd av databaser med hjälp av den sekundära slutpunkten `<fog-name>.secondary.database.windows.net` och utföra alla frågor mot databaserna för att säkerställa anslutning, korrekt säkerhetskonfiguration och datareplikering. 

### <a name="initiate-the-move"></a>Initiera flytten

1. Anslut till målservern med `<fog-name>.secondary.database.windows.net`hjälp av den sekundära slutpunkten .
1. Använd [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) för att växla den sekundära hanterade instansen till att vara den primära med fullständig synkronisering. Den här åtgärden lyckas antingen eller så återställs den. 
1. Kontrollera att kommandot har slutförts `nslook up <fog-name>.secondary.database.windows.net` med hjälp av att kontrollera att DNS CNAME-startpunkterna till IP-adressen för målregionen. Om kommandot switch misslyckas uppdateras inte CNAME. 

### <a name="remove-the-source-databases"></a>Ta bort källdatabaserna

När flytten är klar tar du bort resurserna i källregionen för att undvika onödiga avgifter. 

1. Ta bort redundansgruppen med [Ta bort AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Ta bort varje källdatabas med [Ta bort AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) för var och en av databaserna på källservern. Detta avslutar automatiskt geo-replikeringslänkar. 
1. Ta bort källservern med [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Ta bort nyckelvalvet, granska lagringsbehållare, händelsehubb, AAD-instans och andra beroende resurser för att sluta faktureras för dem. 

## <a name="move-elastic-pools"></a>Flytta elastiska pooler

### <a name="verify-prerequisites"></a>Kontrollera förutsättningar 

1. Skapa en mål logisk server för varje källserver. 
1. Konfigurera brandväggen med rätt undantag med [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Konfigurera de logiska servrarna med rätt inloggningar. Om du inte är prenumerationsadministratör eller SQL-serveradministratör kan du arbeta tillsammans med administratören för att tilldela de behörigheter du behöver. Mer information finns i [Så här hanterar du Azure SQL-databassäkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md). 
1. Om dina databaser krypteras med TDE och använder din egen krypteringsnyckel i Azure-nyckelvalvet, se till att rätt krypteringsmaterial etableras i målregionen.
1. Skapa en elastisk målpool för varje elastisk källpool och se till att poolen skapas på samma tjänstnivå med samma namn och samma storlek. 
1. Om en granskning på databasnivå är aktiverad inaktiverar du den och aktiverar granskning på servernivå i stället. Efter redundans kräver granskning på databasnivå trafik över regiongränserna, vilket inte är önskvärt eller möjligt efter flytten. 
1. För granskningar på servernivå, se till att:
    - Lagringsbehållaren, Logganalysen eller händelsehubben med befintliga granskningsloggar flyttas till målregionen.
    - Granskningskonfigurationen är konfigurerad på målservern. Mer information finns i [SQL-databasgranskning](sql-database-auditing.md).
1. Om din instans har en långsiktig bevarandeprincip (LTR) förblir de befintliga LTR-säkerhetskopiorna associerade med den aktuella servern. Eftersom målservern är annorlunda kan du komma åt de äldre LTR-säkerhetskopiorna i källregionen med hjälp av källservern, även om servern tas bort. 

  > [!NOTE]
  > Detta kommer inte att vara tillräckligt för att flytta mellan det suveräna molnet och en offentlig region. En sådan migrering kräver att LTR-säkerhetskopiorna flyttas till målservern, vilket för närvarande inte stöds. 

### <a name="prepare-to-move"></a>Förbered att flytta
 
1.  Skapa en separat [redundanskitetsgrupp](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) mellan varje elastisk pool på källlogiska servern och dess motsvarighet elastiska pool på målservern. 
1.  Lägg till alla databaser i poolen i redundansgruppen. 
    - Replikering av de tillagda databaserna initieras automatiskt. Mer information finns i [metodtips för redundansk grupper med elastiska pooler](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Även om det är möjligt att skapa en redundansgrupp som innehåller flera elastiska pooler, rekommenderar vi starkt att du skapar en separat redundanskationsgrupp för varje pool. Om du har ett stort antal databaser över flera elastiska pooler som du behöver flytta kan du köra förberedelsestegen parallellt och sedan initiera flyttsteget parallellt. Den här processen skalas bättre och tar mindre tid jämfört med att ha flera elastiska pooler i samma redundansgrupp. 

### <a name="monitor-the-preparation-process"></a>Övervaka förberedelseprocessen

Du kan med jämna mellanrum ringa [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) för att övervaka replikering av dina databaser från källan till målet. Utdataobjektet `Get-AzSqlDatabaseFailoverGroup` innehåller en egenskap för **ReplicationState:** 
   - **ReplicationState = 2** (CATCH_UP) anger att databasen är synkroniserad och kan säkert skickas över. 
   - **ReplicationState = 0** (SEEDNING) anger att databasen ännu inte är sådd och ett försök att redundans misslyckas. 

### <a name="test-synchronization"></a>Testsynkronisering
 
När **ReplicationState** är `2`, ansluta till varje databas, eller delmängd av databaser med hjälp av den sekundära slutpunkten `<fog-name>.secondary.database.windows.net` och utföra alla frågor mot databaserna för att säkerställa anslutning, korrekt säkerhetskonfiguration och datareplikering. 

### <a name="initiate-the-move"></a>Initiera flytten
 
1. Anslut till målservern med `<fog-name>.secondary.database.windows.net`hjälp av den sekundära slutpunkten .
1. Använd [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) för att växla den sekundära hanterade instansen till att vara den primära med fullständig synkronisering. Den här åtgärden lyckas antingen eller så återställs den. 
1. Kontrollera att kommandot har slutförts `nslook up <fog-name>.secondary.database.windows.net` med hjälp av att kontrollera att DNS CNAME-startpunkterna till IP-adressen för målregionen. Om kommandot switch misslyckas uppdateras inte CNAME. 

### <a name="remove-the-source-elastic-pools"></a>Ta bort källreslastade pooler
 
När flytten är klar tar du bort resurserna i källregionen för att undvika onödiga avgifter. 

1. Ta bort redundansgruppen med [Ta bort AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Ta bort varje elastisk källpool på källservern med [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Ta bort källservern med [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Ta bort nyckelvalvet, granska lagringsbehållare, händelsehubb, AAD-instans och andra beroende resurser för att sluta faktureras för dem. 

## <a name="move-managed-instance"></a>Flytta hanterad instans

### <a name="verify-prerequisites"></a>Kontrollera förutsättningar
 
1. För varje källhanterad instans skapa en målhanterad instans av samma storlek i målregionen.  
1. Konfigurera nätverket för en hanterad instans. Mer information finns i [nätverkskonfiguration](sql-database-howto-managed-instance.md#network-configuration).
1. Konfigurera målhuvuddatabasen med rätt inloggningar. Om du inte är prenumerationsadministratör eller SQL-serveradministratör kan du arbeta tillsammans med administratören för att tilldela de behörigheter du behöver. 
1. Om dina databaser krypteras med TDE och använder din egen krypteringsnyckel i Azure-nyckelvalvet kontrollerar du att AKV med identiska krypteringsnycklar finns i både käll- och målregioner. Mer information finns i [TDE med kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Om granskning är aktiverat för instansen, se till att:
    - Lagringsbehållaren eller händelsehubben med befintliga loggar flyttas till målregionen. 
    - Granskning har konfigurerats för målinstansen. Mer information finns i [granskning med hanterad instans](sql-database-managed-instance-auditing.md).
1. Om din instans har en långsiktig bevarandeprincip (LTR) förblir de befintliga LTR-säkerhetskopiorna associerade med den aktuella servern. Eftersom målservern är annorlunda kan du komma åt de äldre LTR-säkerhetskopiorna i källregionen med hjälp av källservern, även om servern tas bort. 

  > [!NOTE]
  > Detta kommer inte att vara tillräckligt för att flytta mellan det suveräna molnet och en offentlig region. En sådan migrering kräver att LTR-säkerhetskopiorna flyttas till målservern, vilket för närvarande inte stöds. 

### <a name="prepare-resources"></a>Förbereda resurser

Skapa en redundansk grupp mellan varje källinstans och motsvarande målinstans.
    - Replikering av alla databaser för varje instans initieras automatiskt. Mer information finns i Grupper för [automatisk redundans.](sql-database-auto-failover-group.md)

 
### <a name="monitor-the-preparation-process"></a>Övervaka förberedelseprocessen

Du kan med jämna mellanrum ringa [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) för att övervaka replikering av dina databaser från källan till målet. Utdataobjektet `Get-AzSqlDatabaseFailoverGroup` innehåller en egenskap för **ReplicationState:** 
   - **ReplicationState = 2** (CATCH_UP) anger att databasen är synkroniserad och kan säkert skickas över. 
   - **ReplicationState = 0** (SEEDNING) anger att databasen ännu inte är sådd och ett försök att redundans misslyckas. 

### <a name="test-synchronization"></a>Testsynkronisering

När **ReplicationState** är `2`, ansluta till varje databas, eller delmängd av databaser med hjälp av den sekundära slutpunkten `<fog-name>.secondary.database.windows.net` och utföra alla frågor mot databaserna för att säkerställa anslutning, korrekt säkerhetskonfiguration och datareplikering. 

### <a name="initiate-the-move"></a>Initiera flytten 

1. Anslut till målservern med `<fog-name>.secondary.database.windows.net`hjälp av den sekundära slutpunkten .
1. Använd [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) för att växla den sekundära hanterade instansen till att vara den primära med fullständig synkronisering. Den här åtgärden lyckas antingen eller så återställs den. 
1. Kontrollera att kommandot har slutförts `nslook up <fog-name>.secondary.database.windows.net` med hjälp av att kontrollera att DNS CNAME-startpunkterna till IP-adressen för målregionen. Om kommandot switch misslyckas uppdateras inte CNAME. 

### <a name="remove-the-source-managed-instances"></a>Ta bort källhanterade instanser
När flytten är klar tar du bort resurserna i källregionen för att undvika onödiga avgifter. 

1. Ta bort redundansgruppen med [Ta bort AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Detta kommer att släppa redundansgruppkonfigurationen och avsluta geo-replikeringslänkar mellan de två instanserna. 
1. Ta bort den källhanterade instansen med [Ta bort AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Ta bort eventuella ytterligare resurser i resursgruppen, till exempel virtuellt kluster, virtuellt nätverk och säkerhetsgrupp. 

## <a name="next-steps"></a>Nästa steg 

[Hantera](sql-database-manage-after-migration.md) din Azure SQL-databas när den har migrerats. 

