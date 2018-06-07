---
title: 'Azure-portalen: SQL Database geo-replikering | Microsoft Docs'
description: Konfigurera geo-replikering för Azure SQL Database i Azure-portalen och initiera redundans
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: e6c699fdd57eecc31c51b26ccb7c06c6edbcfd03
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646906"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurera aktiv geo-replikering för Azure SQL Database i Azure-portalen och initiera redundans

Den här artikeln visar hur du konfigurerar aktiv geo-replikering för SQL-databasen i den [Azure-portalen](http://portal.azure.com) och för att initiera växling vid fel.

Om du vill initiera redundans med Azure-portalen finns [starta en planerad eller oplanerad växling för Azure SQL Database med Azure-portalen](sql-database-geo-replication-portal.md).

Om du vill konfigurera aktiv geo-replikering med hjälp av Azure portal, behöver du följande resurs:

* En Azure SQL database: den primära databasen som du vill replikera till en annan geografisk region.

> [!Note]
Aktiv geo-replikering måste vara mellan databaser i samma prenumeration.

## <a name="add-a-secondary-database"></a>Lägg till en sekundär databas
Följande steg kan du skapa en ny sekundär databas i ett partnerskap geo-replikering.  

Du måste vara prenumerationsägaren eller Medägare för att lägga till en sekundär databas.

Den sekundära databasen har samma namn som den primära databasen och har samma servicenivåer som standard. Den sekundära databasen kan vara en enskild databas eller en databas i en elastisk pool. Mer information finns i [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md).
När sekundärt skapas och dirigeras, börjar data replikeras från den primära databasen till den nya sekundära databasen.

> [!NOTE]
> Kommandot misslyckas om partner-databas redan finns (till exempel till följd av avslutar relationen för en tidigare geo-replikering).
> 

1. I den [Azure-portalen](http://portal.azure.com), bläddra till den databas som du vill konfigurera för geo-replikering.
2. På sidan SQL-databasen väljer **georeplikering**, och välj sedan regionen för att skapa den sekundära databasen. Du kan välja en region än den region som är värd för den primära databasen, men vi rekommenderar den [parad region](../best-practices-availability-paired-regions.md).
   
    ![Konfigurera geo-replikering](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Välj eller konfigurera servern och prisnivå för den sekundära databasen.
   
    ![Konfigurera sekundära](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Alternativt kan du lägga till en sekundär databas till en elastisk pool. Klicka för att skapa den sekundära databasen i en pool **elastisk pool** och välj en pool på målservern. En pool måste redan finnas på målservern. Det här arbetsflödet kan inte skapa en pool.
5. Klicka på **skapa** att lägga till sekundärt.
6. Den sekundära databasen skapas och seeding-processen börjar.
   
    ![Konfigurera sekundära](./media/sql-database-geo-replication-portal/seeding0.png)
7. När seeding processen är klar visas den sekundära databasen dess status.
   
    ![Seeding klar](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Initiera redundans

Den sekundära databasen kan växlas till primärt.  

1. I den [Azure-portalen](http://portal.azure.com), bläddra till den primära databasen i partnerskap geo-replikering.
2. På bladet SQL-databasen väljer **alla inställningar** > **georeplikering**.
3. I den **SEKUNDÄRSERVRAR** väljer du den databas du vill bli den nya primärt och på **redundans**.
   
    ![växling vid fel](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Klicka på **Ja** starta redundansväxlingen.

Kommandot växlar omedelbart den sekundära databasen till den primära rollen. 

Det finns en kort period under vilken båda databaserna är inte tillgänglig (terabyte 0 till 25 sekunder) när rollerna växlas. Om den primära databasen har flera sekundära databaser, automatiskt de andra sekundärservrar för att ansluta till den nya primärt automatiskt om kommandot. Hela åtgärden bör ta mindre än en minut att slutföra under normala omständigheter. 

> [!NOTE]
> Det här kommandot har utformats för snabb återställning av databasen vid ett strömavbrott. Utlöser redundans utan datasynkronisering (tvingas redundans).  Om den primära servern är online och acceptera transaktioner när kommandot utfärdas dataförluster kan uppstå. 
> 
> 

## <a name="remove-secondary-database"></a>Ta bort sekundära databas
Den här åtgärden permanent avslutar replikering till den sekundära databasen och ändras på sekundärt rollen till en vanlig skrivskyddad databas. Om anslutningen till den sekundära databasen är bruten kommandot lyckas men sekundära har inte blir oskyddad förrän anslutningen har återställts.  

1. I den [Azure-portalen](http://portal.azure.com), bläddra till den primära databasen i partnerskap geo-replikering.
2. På sidan SQL-databasen väljer **georeplikering**.
3. I den **SEKUNDÄRSERVRAR** Markera databasen som du vill ta bort från partnerskap för geo-replikering.
4. Klicka på **Replikeringsstopp**.
   
    ![Ta bort sekundär](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. En bekräftelse öppnas. Klicka på **Ja** att ta bort databasen från partnerskap för geo-replikering. (Ange det till en skrivskyddad databas inte en del av all replikering.)

## <a name="next-steps"></a>Nästa steg
* Mer information om aktiv geo-replikering finns [aktiv geo-replikering](sql-database-geo-replication-overview.md).
* En översikt över verksamhetskontinuitet och scenarier finns [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).

