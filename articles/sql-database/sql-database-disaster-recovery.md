---
title: Haveriberedskap för SQL-databas | Microsoft Docs
description: Lär dig mer om att återställa en databas från en regionala datacenter-avbrott eller ett fel med Azure SQL Database aktiv geo-replikering och funktioner för geo-återställning.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: ba462d1c04f9a45932716fe07cbf1b7298d13cdb
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268674"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Återställa en Azure SQL Database eller redundans till en sekundär

Azure SQL Database erbjuder följande funktioner för att återställa från ett avbrott:

- [Aktiv geo-replikering](sql-database-active-geo-replication.md)
- [Automatisk redundans grupper](sql-database-auto-failover-group.md)
- [GEO-återställning](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Zonredundanta databaser](sql-database-high-availability.md)

Läs mer om affärskontinuitet företagsscenarier och de funktioner som stöder dessa scenarier, i [affärskontinuitet](sql-database-business-continuity.md).

> [!NOTE]
> Om du använder zonredundant Premium eller affärskritiska databaser eller pooler återställningen sker automatiskt och resten av detta material gäller inte.

## <a name="prepare-for-the-event-of-an-outage"></a>Förbereda för händelse av ett avbrott

För att lyckas med återställning till en annan dataregion med redundansgrupper eller geo-redundanta säkerhetskopieringar, måste du förbereda en server i ett annat Datacenter avbrott ska bli den nya primära servern bör behovet uppstår samt har väldefinierade stegen och testats för att säkerställa en smidig återställning. Dessa förberedelsesteg är:

- Identifiera den logiska servern i en annan region ska bli den nya primära servern. För geo-återställning, det är vanligtvis en server i den [parad region](../best-practices-availability-paired-regions.md) för regionen där databasen är placerad. Detta eliminerar kostnaden för ytterligare trafik under återställning av geo-åtgärder.
- Identifiera och du kan också definiera regler för brandväggen på servernivå som behövs på användare får åtkomst till den nya primära databasen.
- Avgör hur du kommer att omdirigera användare till den nya primära servern, till exempel genom att ändra anslutningssträngar eller genom att ändra DNS-poster.
- Identifiera och du kan också skapa inloggningar som måste finnas i master-databasen på den nya primära servern och se till att dessa inloggningar har rätt behörighet i master-databasen, om sådana. Mer information finns i [SQL Database-säkerhet efter återställning](sql-database-geo-replication-security-config.md)
- Identifiera Varningsregler som måste uppdateras för att mappa till den nya primära databasen.
- Dokumentera granskning konfigurationen på den aktuella primära databasen
- Utföra en [programåterställningstest](sql-database-disaster-recovery-drills.md). Om du vill simulera ett avbrott för geo-återställning kan du ta bort eller byta namn på källdatabasen för att orsaka anslutningsfel för program. Om du vill simulera ett avbrott med redundansgrupper kan inaktivera du webbprogram eller virtuella datorn är ansluten till databasen eller redundans databasen för att orsaka anslutningsfel för program.

## <a name="when-to-initiate-recovery"></a>När du vill initiera återställning

Återställningen påverkar programmet. Det innebär att du ändrar SQL-anslutningssträng eller genom att använda DNS och kan leda till förlust av data permanent. Det bör därför utföras endast när avbrottet troligen kommer att räcka längre än återställningstid för ditt program. När programmet distribueras till produktion bör du utföra regelbundna övervakning av programmets hälsotillstånd och Använd följande datapunkter för att bekräfta att återställningen är korrekt:

1. Permanent anslutningsfel från programmet-nivån till databasen.
2. Azure-portalen visar en avisering om en incident i regionen med stor inverkan.

> [!NOTE]
> Om du använder grupper för växling vid fel och har valt automatisk växling vid fel är återställningsprocessen automatiskt och transparent för programmet.

Beroende på toleransen programmet driftstopp och möjliga företag ansvar du följande återställningsalternativ.

Använd den [får återställa databasen](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) att hämta den senaste Geo-replikerade återställningspunkten.

## <a name="wait-for-service-recovery"></a>Vänta tills vbetjäningsåterställning

Azure teams arbete hårt för att återställa tjänsttillgänglighet så snabbt som möjligt, men beroende på roten som orsakar det kan ta flera timmar eller dagar.  Om ditt program kan tolerera betydande driftavbrott kan du helt enkelt vänta på att slutföra återställningen. I så fall krävs ingen åtgärd från din sida. Du kan se aktuell status för tjänsten på vår [Hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/). Efter återställningen av regionen återställs programmets tillgänglighet.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Växla över till geo-replikerad sekundär server i redundansgruppen

Om ditt programs avbrott kan resultera i företag ansvar, bör du använda grupper för växling vid fel. Det gör det möjligt för program att snabbt återställa anslutningen i en annan region vid ett strömavbrott. En självstudiekurs finns i [implementera en geodistribuerad databas](sql-database-implement-geo-distributed-database.md).

För att återställa anslutningen till databaserna måste du initiera redundans till den sekundära servern med någon av metoderna som stöds.

Använd någon av följande guider för att växla över till en geo-replikerad sekundär databas:

- [Växla över till en geo-replikerad sekundär server med Azure portal](sql-database-geo-replication-portal.md)
- [Växla över till den sekundära servern med hjälp av PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)

## <a name="recover-using-geo-restore"></a>Återställa med geo-återställning

Om ditt programs driftstopp inte resulterar i ansvar för företag kan du använda [geo-återställning](sql-database-recovery-using-backups.md) som en metod för att återställa dina program-databaser. Den skapar en kopia av databasen från dess senaste geo-redundant säkerhetskopia.

## <a name="configure-your-database-after-recovery"></a>Konfigurera databasen efter återställningen

Om du använder geo-återställning för att återställa från ett avbrott, måste du kontrollera att anslutningen till de nya databaserna är korrekt konfigurerad så att funktionen normala program kan återupptas. Det här är en checklistan med uppgifter som du förbereder din återställda databas i produktionsmiljön.

### <a name="update-connection-strings"></a>Uppdatera anslutningssträngar

Eftersom din återställda databas finns i en annan server, måste du uppdatera anslutningssträngen för ditt program så att den pekar till den servern.

Mer information om hur du ändrar anslutningssträngar finns i lämplig programmeringsspråk för din [anslutningsbibliotek](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Konfigurera brandväggsregler

Du måste se till att brandväggsreglerna som konfigurerats på servern och på databasen matchar de som har konfigurerats på den primära servern och den primära databasen. Mer information finns i [så här: Konfigurera brandväggsinställningar (Azure SQL Database)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Konfigurera inloggningar och användare

Du måste kontrollera att alla inloggningar som används av ditt program finns på servern som är värd för din återställda databas. Mer information finns i [säkerhetskonfiguration för geo-replikering](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Du bör konfigurera och testa dina brandväggsregler för servern och inloggningar (och deras behörigheter) under ett programåterställningstest. Objekten på servernivå och deras konfiguration kan inte vara tillgängliga under avbrottet.

### <a name="setup-telemetry-alerts"></a>Konfigurera aviseringar för telemetri

Du måste kontrollera att din befintliga varningsregel-inställningarna har uppdaterats för att mappa till den återställda databasen och annan server.

Läs mer om databasen Varningsregler [ta emot aviseringen meddelanden](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) och [spåra Tjänstehälsa](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Aktivera granskning

Om granskning krävs för att få åtkomst till din databas, måste du aktivera granskning efter databasåterställningen. Mer information finns i [databasgranskning](sql-database-auditing.md).

## <a name="next-steps"></a>Nästa steg

- Vill veta mer om Azure SQL Database automatiska säkerhetskopior, se [automatiserad säkerhetskopiering för SQL-databas](sql-database-automated-backups.md)
- Läs om business continuity design- och återställningsscenarier i [affärskontinuitet scenarier](sql-database-business-continuity.md)
- Läs om hur du använder automatiska säkerhetskopieringar för återställning i [återställa en databas från de tjänstinitierade säkerhetskopiorna](sql-database-recovery-using-backups.md)
