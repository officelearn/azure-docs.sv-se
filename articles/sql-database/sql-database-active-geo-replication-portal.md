---
title: 'Azure-portalen: SQL Database geo-replikering | Microsoft Docs'
description: Konfigurera geo-replikering för en databas med en eller flera databaser i Azure SQL Database att använda Azure Portal och initiera redundans
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 058afdbc4aa134b5b3c4c8cc5e9e2f2ae6f53084
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569641"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurera aktiv geo-replikering för Azure SQL Database i Azure Portal och initiera redundans

Den här artikeln visar hur du konfigurerar [aktiv geo-replikering för enstaka och](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) grupperade databaser i Azure SQL Database att använda [Azure Portal](https://portal.azure.com) och för att initiera redundans.

Information om grupper för automatisk redundans med enkla databaser och databaser i pooler finns i [metod tips för att använda failover-grupper med enkla databaser och databaser i pooler](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Information om grupper för automatisk redundans med hanterade instanser (förhands granskning) finns i [metod tips för att använda failover-grupper med hanterade instanser](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera aktiv geo-replikering med hjälp av Azure Portal behöver du följande resurs:

* En Azure SQL-databas: Den primära databasen som du vill replikera till en annan geografisk region.

> [!Note]
> När du använder Azure Portal kan du bara skapa en sekundär databas inom samma prenumeration som den primära. Om den sekundära databasen måste finnas i en annan prenumeration använder du [create database REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) eller [Alter Database Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Lägg till en sekundär databas

Följande steg skapar en ny sekundär databas i en partnerskap för geo-replikering.  

Om du vill lägga till en sekundär databas måste du vara prenumerations ägare eller medägare.

Den sekundära databasen har samma namn som den primära databasen och har som standard samma tjänste nivå och beräknings storlek. Den sekundära databasen kan vara en enskild databas eller en databas i poolen. Mer information finns i [DTU-baserad inköps modell](sql-database-service-tiers-dtu.md) och [vCore-baserad inköps modell](sql-database-service-tiers-vcore.md).
När den sekundära har skapats och dirigerats påbörjar data replikeringen från den primära databasen till den nya sekundära databasen.

> [!NOTE]
> Om partner databasen redan finns (till exempel som ett resultat av en tidigare geo-replikeringsrelation) Miss lyckas kommandot.

1. I [Azure Portal](https://portal.azure.com)bläddrar du till den databas som du vill konfigurera för geo-replikering.
2. På sidan SQL-databas väljer du **geo-replikering**och väljer sedan den region där du vill skapa den sekundära databasen. Du kan välja en annan region än den region som är värd för den primära databasen, men vi rekommenderar den [kopplade regionen](../best-practices-availability-paired-regions.md).

    ![Konfigurera geo-replikering](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Välj eller konfigurera server och pris nivå för den sekundära databasen.

    ![Konfigurera sekundär](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Alternativt kan du lägga till en sekundär databas i en elastisk pool. Om du vill skapa den sekundära databasen i en pool klickar du på **elastisk pool** och väljer en pool på mål servern. En pool måste redan finnas på mål servern. Det här arbets flödet skapar inte någon pool.
5. Klicka på **skapa** för att lägga till den sekundära.
6. Den sekundära databasen skapas och initierings processen påbörjas.

    ![Konfigurera sekundär](./media/sql-database-geo-replication-portal/seeding0.png)
7. När initierings processen är klar visas dess status i den sekundära databasen.

    ![Dirigering har slutförts](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Initiera en redundansväxling

Den sekundära databasen kan växlas till den primära.  

1. I [Azure Portal](https://portal.azure.com)bläddrar du till den primära databasen i partnerskapet för geo-replikering.
2. På bladet SQL Database väljer du **alla inställningar** > **geo-replikering**.
3. I listan **sekundära** väljer du den databas som du vill bli den nya primära och klickar på **redundans**.

    ![redundans](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Klicka på **Ja** för att starta redundansväxlingen.

Kommandot växlar omedelbart den sekundära databasen till den primära rollen. Den här processen bör normalt slutföras inom 30 SEK eller mindre.

Det finns en kort period under vilken båda databaserna inte är tillgängliga (i ordningen 0 till 25 sekunder) När rollerna växlas. Om den primära databasen har flera sekundära databaser konfigurerar kommandot automatiskt om de andra sekundärerna för att ansluta till den nya primära servern. Hela åtgärden bör ta mindre än en minut att slutföra under normala omständigheter.

> [!NOTE]
> Det här kommandot har utformats för snabb återställning av databasen i händelse av ett avbrott. Den utlöser redundans utan datasynkronisering (framtvingad redundansväxling).  Om den primära är online och genomför transaktioner när kommandot utfärdas kan viss data förlust uppstå.

## <a name="remove-secondary-database"></a>Ta bort sekundär databas

Den här åtgärden avslutar replikeringen till den sekundära databasen permanent och ändrar rollen för den sekundära till en vanlig Läs-och skriv databas. Om anslutningen till den sekundära databasen bryts, lyckas kommandot men den sekundära blir inte skrivskyddad förrän anslutningen har återställts.  

1. I [Azure Portal](https://portal.azure.com)bläddrar du till den primära databasen i partnerskapet för geo-replikering.
2. På SQL Database-sidan väljer du **geo-replikering**.
3. I listan **sekundära** väljer du den databas som du vill ta bort från partnerskapet för geo-replikering.
4. Klicka på **stoppa replikering**.

    ![Ta bort sekundär](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Ett bekräftelse fönster öppnas. Klicka på **Ja** om du vill ta bort databasen från partnerskapet för geo-replikering. (Ange den som en skrivskyddad databas som inte tillhör någon replikering.)

## <a name="next-steps"></a>Nästa steg

* Mer information om aktiv geo-replikering finns i [aktiv geo-replikering](sql-database-active-geo-replication.md).
* Information om grupper för automatisk redundans finns i [grupper för automatisk redundans](sql-database-auto-failover-group.md)
* En översikt över kontinuitet och scenarier för affärs kontinuitet finns i [Översikt över verksamhets kontinuitet](sql-database-business-continuity.md).
