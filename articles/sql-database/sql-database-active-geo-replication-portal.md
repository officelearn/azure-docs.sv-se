---
title: 'Självstudiekurs: Geo-replikering & redundans i portalen'
description: Konfigurera georeplikering för en enda eller poolad databas i Azure SQL Database med Azure-portalen och initiera redundans.
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
ms.openlocfilehash: 59616fb217b28a8c47d9a5d13e2f4c1b9a8f6bb3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605231"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurera aktiv geo-replikering för Azure SQL Database i Azure-portalen och initiera redundans

Den här artikeln visar hur du konfigurerar [aktiv geo-replikering för enskilda och poolade databaser](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) i Azure SQL Database med Hjälp av [Azure-portalen](https://portal.azure.com) och för att initiera redundans.

Information om grupper för automatisk redundans med enstaka och poolade databaser finns i [Metodtips för att använda redundansklar med enstaka och poolade databaser](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Information om grupper för automatisk redundans med hanterade instanser finns i [Metodtips för att använda redundanskörer med hanterade instanser](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera aktiv geo-replikering med hjälp av Azure-portalen behöver du följande resurs:

* En Azure SQL-databas: Den primära databasen som du vill replikera till ett annat geografiskt område.

> [!Note]
> När du använder Azure-portalen kan du bara skapa en sekundär databas inom samma prenumeration som den primära. Om sekundär databas krävs för att vara i en annan prenumeration använder du [Skapa rest-API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) för databas eller [ALTER DATABASE Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Lägga till en sekundär databas

I följande steg skapas en ny sekundär databas i ett partnerskap för georeplikering.  

Om du vill lägga till en sekundär databas måste du vara prenumerationsägare eller delägare.

Den sekundära databasen har samma namn som den primära databasen och har som standard samma tjänstnivå och beräkningsstorlek. Den sekundära databasen kan vara en enda databas eller en poolad databas. Mer information finns i [DTU-baserad inköpsmodell](sql-database-service-tiers-dtu.md) och [vCore-baserad inköpsmodell](sql-database-service-tiers-vcore.md).
När den sekundära har skapats och dirigerats börjar data replikera från den primära databasen till den nya sekundära databasen.

> [!NOTE]
> Om partnerdatabasen redan finns (till exempel som ett resultat av att en tidigare geo-replikeringsrelation har avslutats) misslyckas kommandot.

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till den databas som du vill konfigurera för geo-replikering.
2. På SQL-databassidan väljer du **geo-replikering**och väljer sedan det område som ska skapa den sekundära databasen. Du kan välja en annan region än den region som är värd för den primära databasen, men vi rekommenderar den [parade regionen](../best-practices-availability-paired-regions.md).

    ![Konfigurera geo-replikering](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Välj eller konfigurera servern och prisnivån för den sekundära databasen.

    ![Konfigurera sekundärt](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Du kan också lägga till en sekundär databas i en elastisk pool. Om du vill skapa den sekundära databasen i en pool klickar du på **den elastiska poolen** och väljer en pool på målservern. Det måste redan finnas en pool på målservern. Det här arbetsflödet skapar ingen pool.
5. Klicka på **Skapa** om du vill lägga till den sekundära.
6. Den sekundära databasen skapas och dirigeringsprocessen börjar.

    ![Konfigurera sekundärt](./media/sql-database-geo-replication-portal/seeding0.png)
7. När dirigeringsprocessen är klar visar den sekundära databasen sin status.

    ![Sådd klar](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Initiera en redundans

Den sekundära databasen kan växlas till att bli den primära.  

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till den primära databasen i partnerskapet för georeplikering.
2. På SQL Database-bladet väljer du **Alla inställningar** > **geo-replikering**.
3. I listan **SEKUNDÄRDE** väljer du den databas som du vill ska bli den nya primära och klickar på **Tvingad redundans**.

    ![Failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Klicka på **Ja** för att påbörja redundansen.

Kommandot växlar omedelbart den sekundära databasen till den primära rollen. Denna process bör normalt slutföras inom 30 sek eller mindre.

Det finns en kort period under vilken båda databaserna inte är tillgängliga (i ordningen 0 till 25 sekunder) medan rollerna växlas. Om den primära databasen har flera sekundära databaser konfigureras de andra sekundärfilerna automatiskt om för att ansluta till den nya primär. Hela operationen bör ta mindre än en minut att slutföra under normala omständigheter.

> [!NOTE]
> Det här kommandot är utformat för snabb återställning av databasen vid ett avbrott. Det utlöser redundans utan datasynkronisering (påtvingad redundans).  Om den primära är online och genomför transaktioner när kommandot utfärdas kan en del dataförlust uppstå.

## <a name="remove-secondary-database"></a>Ta bort sekundär databas

Den här åtgärden avslutar replikeringen permanent till den sekundära databasen och ändrar rollen för den sekundära till en vanlig läs-skriv-databas. Om anslutningen till den sekundära databasen bryts lyckas kommandot, men det sekundära läs-skrivs inte förrän anslutningen har återställts.  

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till den primära databasen i partnerskapet för georeplikering.
2. Välj **geo-replikering**på SQL-databassidan .
3. I listan **SECONDARIES** väljer du den databas som du vill ta bort från partnerskapet för georeplikering.
4. Klicka på **Stoppa replikering**.

    ![Ta bort sekundär](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Ett bekräftelsefönster öppnas. Klicka på **Ja** om du vill ta bort databasen från geo-replikeringspartnerskapet. (Ställ in den på en läs-skriv-databas som inte ingår i någon replikering.)

## <a name="next-steps"></a>Nästa steg

* Mer information om aktiv georeplikering finns i [aktiv georeplikering](sql-database-active-geo-replication.md).
* Mer information om grupper för automatisk redundans finns i [Grupper för automatisk redundans](sql-database-auto-failover-group.md)
* En översikt över affärskontinuitet och scenarier finns i [Översikt över affärskontinuitet](sql-database-business-continuity.md).
