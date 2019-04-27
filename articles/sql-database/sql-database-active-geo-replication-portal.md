---
title: 'Azure-portalen: SQL Database geo-replikering | Microsoft Docs'
description: Konfigurera geo-replikering för en enskild eller grupperade databas i Azure SQL Database med hjälp av Azure-portalen och initiera redundans
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 8bada96c648881a9943176c45115627a829fcc58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864140"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurera aktiv geo-replikering för Azure SQL Database i Azure-portalen och initiera redundans

Den här artikeln visar hur du konfigurerar [aktiv geo-replikering för enkel och delade databaser](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) i Azure SQL Database med hjälp av den [Azure-portalen](https://portal.azure.com) och för att initiera redundans.

Information om automatisk redundans grupper med enkel och delade databaser finns i [bästa praxis att använda grupper för växling vid fel med enkel och delade databaser](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Information om automatisk redundans grupper med hanterade instanser (förhandsversion) finns i [bästa praxis att använda grupper för växling vid fel med hanterade instanser](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera aktiv geo-replikering med hjälp av Azure portal, behöver du följande resurser:

* En Azure SQL database: Den primära databasen som du vill replikera till en annan geografisk region.

> [!Note]
> När du använder Azure-portalen kan skapa du bara en sekundär databas i samma prenumeration som primärt. Om sekundär databas måste vara i en annan prenumeration, använder [Create Database REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) eller [ALTER DATABASE Transact-SQL API: et](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Lägg till en sekundär databas

Följande steg kan du skapa en ny sekundär databas i en koppling för geo-replikering.  

Om du vill lägga till en sekundär databas, måste du vara Prenumerationens ägare eller Medägare.

Den sekundära databasen har samma namn som den primära databasen och som standard har samma tjänst, nivå och beräkna storleken. Den sekundära databasen kan vara en enkel databas eller en databas i pool. Mer information finns i [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).
När sekundärt skapas och dirigeras, börjar data replikeras från den primära databasen till den nya sekundära databasen.

> [!NOTE]
> Kommandot misslyckas om partner-databasen finns redan (till exempel till följd av avslutar en föregående geo-replikeringsrelation).

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den databas som du vill konfigurera för geo-replikering.
2. SQL database-sidan Välj **geo-replikering**, och välj sedan regionen du skapar den sekundära databasen. Du kan välja vilken region som helst utom den region som är värd för den primära databasen, men vi rekommenderar den [parad region](../best-practices-availability-paired-regions.md).

    ![Konfigurera geo-replikering](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Välj eller konfigurera servern och prisnivå för den sekundära databasen.

    ![Konfigurera sekundär](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Du kan också lägga till en sekundär databas i en elastisk pool. Klicka för att skapa den sekundära databasen i en pool **elastisk pool** och välj en pool på målservern. En pool måste redan finnas på målservern. Det här arbetsflödet kan inte skapa en pool.
5. Klicka på **skapa** att lägga till sekundärt.
6. Den sekundära databasen skapas och seeding-processen börjar.

    ![Konfigurera sekundär](./media/sql-database-geo-replication-portal/seeding0.png)
7. När seeding processen är klar visas den sekundära databasen dess status.

    ![Seeding klar](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Initiera redundans

Den sekundära databasen kan stängas för att bli primärt.  

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den primära databasen i partnerskapet för geo-replikering.
2. På bladet SQL Database väljer **alla inställningar** > **geo-replikering**.
3. I den **SEKUNDÄRSERVRAR** väljer du den databas du vill bli den nya primärt och på **redundans**.

    ![redundans](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Klicka på **Ja** starta redundansväxlingen.

Kommandot växlar omedelbart sekundär databas till den primära rollen. Den här processen är normalt bör slutföras inom 30 sekunder eller mindre.

Det finns en kort tidsperiod under vilken båda databaserna är inte tillgänglig (i storleksordningen 0 till 25 sekunder) när rollerna växlas. Om den primära databasen har flera sekundära databaser, konfigurerar kommandot automatiskt om de andra sekundärservrar för att ansluta till den nya primärt. Hela åtgärden tar normalt mindre än en minut att slutföra under normala omständigheter.

> [!NOTE]
> Det här kommandot har utformats för snabb återställning av databasen vid ett strömavbrott. Den utlöser redundans utan datasynkronisering (forcerad redundans).  Om den primära servern är online och acceptera transaktioner när kommandot ges viss dataförlust kan inträffa.

## <a name="remove-secondary-database"></a>Ta bort sekundär databas

Den här åtgärden permanent avslutar replikeringen till den sekundära databasen och ändras sekundärt rollen till en vanlig skrivskyddad databas. Om anslutningen till den sekundära databasen är bruten kommandot lyckas men sekundära stöder inte blir skrivskyddade förrän efter anslutningen har återställts.  

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den primära databasen i partnerskapet för geo-replikering.
2. SQL database-sidan Välj **geo-replikering**.
3. I den **SEKUNDÄRSERVRAR** väljer du den databas som du vill ta bort från partnerskapet geo-replikering.
4. Klicka på **Replikeringsstopp**.

    ![Ta bort sekundär](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Ett bekräftelsefönster öppnas. Klicka på **Ja** att ta bort databasen från geo-replikering-partnerskap. (Ange den till en oskyddad databas inte en del av all replikering.)

## <a name="next-steps"></a>Nästa steg

* Mer information om aktiv geo-replikering finns [aktiv geo-replikering](sql-database-active-geo-replication.md).
* Läs mer om automatisk redundans grupper, i [automatisk redundans grupper](sql-database-auto-failover-group.md)
* En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md).
