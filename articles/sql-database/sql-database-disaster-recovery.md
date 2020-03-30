---
title: Haveriberedskap
description: Lär dig hur du återställer en databas från ett regionalt datacenteravbrott eller ett fel med azure SQL Database active geo-replication och geo-restore-funktioner.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/21/2019
ms.openlocfilehash: d28edd28dcbe31bfe63c2d0a9c3e975967efef04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256385"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Återställa en Azure SQL-databas eller redundans till en sekundär

Azure SQL Database erbjuder följande funktioner för återställning från ett avbrott:

- [Aktiv geo-replikering](sql-database-active-geo-replication.md)
- [Automatiska redundansgrupper](sql-database-auto-failover-group.md)
- [Geo-återställande](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Zonundanta databaser](sql-database-high-availability.md)

Mer information om scenarier för affärskontinuitet och de funktioner som stöder dessa scenarier finns i [Kontinuitet i verksamheten](sql-database-business-continuity.md).

> [!NOTE]
> Om du använder zonsantat Premium- eller Affärskritiska databaser eller pooler automatiseras återställningsprocessen och resten av det här materialet gäller inte.

> [!NOTE]
> Både primära och sekundära databaser måste ha samma tjänstnivå. Vi rekommenderar också starkt att den sekundära databasen skapas med samma beräkningsstorlek (DTUs eller virtuella kärnor) som den primära. Mer information finns i [Uppgradera eller nedgradera som primär databas](sql-database-active-geo-replication.md#upgrading-or-downgrading-primary-database).

> [!NOTE]
> Använd en eller flera redundansklar för att hantera redundans för flera databaser.
> Om du lägger till en befintlig geo-replikeringsrelation i redundansgruppen kontrollerar du att den geografiska sekundärt är konfigurerad med samma tjänstnivå och beräkningsstorlek som primär. Mer information finns i [Använda grupper för automatisk redundans för att aktivera genomskinlig och samordnad redundans för flera databaser](sql-database-auto-failover-group.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Förbered dig för ett avbrott

För att lyckas med återställning till en annan dataregion med hjälp av antingen redundansgrupper eller geoupptäckta säkerhetskopior måste du förbereda en server i ett annat datacenter avbrott för att bli den nya primära servern om behov skulle uppstå samt ha väldefinierade steg dokumenterade och testas för att säkerställa en smidig återhämtning. Dessa förberedelsesteg omfattar:

- Identifiera SQL Database-servern i en annan region för att bli den nya primära servern. För geoåterställning är detta i allmänhet en server i den [parade regionen](../best-practices-availability-paired-regions.md) för den region där databasen finns. Detta eliminerar den extra trafikkostnaden under geo-återställande åtgärder.
- Identifiera och eventuellt definiera de IP-brandväggsregler på servernivå som behövs för att användarna ska kunna komma åt den nya primära databasen.
- Bestäm hur du ska omdirigera användare till den nya primära servern, till exempel genom att ändra anslutningssträngar eller genom att ändra DNS-poster.
- Identifiera och eventuellt skapa de inloggningar som måste finnas i huvuddatabasen på den nya primära servern och se till att dessa inloggningar har lämpliga behörigheter i huvuddatabasen, om sådana finns. Mer information finns i [SQL Database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md)
- Identifiera varningsregler som måste uppdateras för att mappa till den nya primära databasen.
- Dokumentera granskningskonfigurationen i den aktuella primära databasen
- Utför en [borr för haveriberedskap](sql-database-disaster-recovery-drills.md). Om du vill simulera ett avbrott för geoåterställning kan du ta bort eller byta namn på källdatabasen för att orsaka anslutningsfel för programmet. Om du vill simulera ett avbrott med redundansgrupper kan du inaktivera webbprogrammet eller den virtuella datorn som är ansluten till databasen eller redundansdatabasen för att orsaka programanslutningsfel.

## <a name="when-to-initiate-recovery"></a>När ska återställningen initieras

Återställningsåtgärden påverkar programmet. Det kräver att ändra SQL-anslutningssträngen eller omdirigering med DNS och kan resultera i permanent dataförlust. Därför bör det endast göras när avbrottet sannolikt kommer att pågå längre än programmets återställningstidsmål. När programmet distribueras till produktion bör du utföra regelbunden övervakning av programmets hälsa och använda följande datapunkter för att hävda att återställningen är berättigad:

1. Permanent anslutningsfel från programnivån till databasen.
2. Azure-portalen visar en avisering om en incident i regionen med bred inverkan.

> [!NOTE]
> Om du använder redundanskationsgrupper och väljer automatisk redundans är återställningsprocessen automatiserad och transparent för programmet.

Beroende på din ansökan tolerans för driftstopp och eventuellt affärsansvar kan du överväga följande återställningsalternativ.

Använd [Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) *(LastAvailableBackupDate)* för att hämta den senaste geo-replikerade återställningspunkten.

## <a name="wait-for-service-recovery"></a>Vänta på återställning av tjänsten

Azure-teamen arbetar hårt för att återställa tjänstens tillgänglighet så snabbt som möjligt, men beroende på grundorsaken kan det ta timmar eller dagar.  Om ditt program kan tolerera betydande driftstopp kan du helt enkelt vänta på att återställningen ska slutföras. I det här fallet krävs ingen åtgärd från din sida. Du kan se aktuell tjänststatus på vår [Azure Service Health Dashboard](https://azure.microsoft.com/status/). Efter återställningen av regionen återställs programmets tillgänglighet.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Växla över till geografisk replikerad sekundär server i redundansgruppen

Om programmets driftstopp kan leda till affärsansvar bör du använda redundansk grupper. Det gör det möjligt för programmet att snabbt återställa tillgängligheten i en annan region i händelse av ett avbrott. En självstudiekurs finns i [Implementera en geodistribuerad databas](sql-database-implement-geo-distributed-database.md).

Om du vill återställa tillgängligheten för databaserna måste du initiera redundansen till den sekundära servern med någon av de metoder som stöds.

Använd någon av följande guider för att växla över till en geografisk replikerad sekundär databas:

- [Växla över till en geografiskt replikerad sekundär server med Azure-portalen](sql-database-geo-replication-portal.md)
- [Växla över till den sekundära servern med PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Växla över till en sekundär server med Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Återställ med geoåterställning

Om programmets driftstopp inte leder till affärsansvar kan du använda [geoåterställning](sql-database-recovery-using-backups.md) som en metod för att återställa programdatabaserna. Det skapar en kopia av databasen från den senaste geo-redundanta säkerhetskopian.

## <a name="configure-your-database-after-recovery"></a>Konfigurera databasen efter återställning

Om du använder geoåterställning för att återställa från ett avbrott måste du se till att anslutningen till de nya databaserna är korrekt konfigurerad så att den normala programfunktionen kan återupptas. Det här är en checklista med uppgifter för att förbereda den återställda databasproduktionen.

### <a name="update-connection-strings"></a>Uppdatera anslutningssträngar

Eftersom den återställda databasen finns på en annan server måste du uppdatera programmets anslutningssträng så att den pekar på den servern.

Mer information om hur du ändrar anslutningssträngar finns i lämpligt utvecklingsspråk för [anslutningsbiblioteket](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Konfigurera brandväggsregler

Du måste se till att brandväggsreglerna som konfigurerats på servern och i databasen matchar de som har konfigurerats på den primära servern och den primära databasen. Mer information finns i [Så här konfigurerar du brandväggsinställningar (Azure SQL Database).](sql-database-configure-firewall-settings.md)

### <a name="configure-logins-and-database-users"></a>Konfigurera inloggningar och databasanvändare

Du måste se till att alla inloggningar som används av ditt program finns på servern som är värd för din återställda databas. Mer information finns i [Säkerhetskonfiguration för geo-replikering](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Du bör konfigurera och testa serverns brandväggsregler och inloggningar (och deras behörigheter) under en övning för haveriberedskap. Dessa servernivåobjekt och deras konfiguration kanske inte är tillgängliga under avbrottet.

### <a name="setup-telemetry-alerts"></a>Konfigurera telemetrivarningar

Du måste se till att dina befintliga inställningar för varningsregeln uppdateras för att mappa till den återställda databasen och den olika servern.

Mer information om databasaviseringsregler finns i [Ta emot aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) och spåra [tjänsthälsa](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Aktivera granskning

Om granskning krävs för att komma åt databasen måste du aktivera Granskning efter databasåterställning. Mer information finns i [Databasgranskning](sql-database-auditing.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om automatiska säkerhetskopior i Azure SQL Database finns i [automatiska säkerhetskopieringar i SQL Database](sql-database-automated-backups.md)
- Mer information om design- och återställningsscenarier för affärskontinuitet finns i [Kontinuitetsscenarier](sql-database-business-continuity.md)
- Mer information om hur du använder automatiska säkerhetskopior för återställning finns i [återställa en databas från de tjänstinitierade säkerhetskopiorna](sql-database-recovery-using-backups.md)
