---
title: "SQL Database-katastrofåterställning | Microsoft Docs"
description: "Lär dig hur du återställer en databas från en regionala datacenter avbrott eller ett fel med Azure SQL Database aktiv geo-replikering och funktioner för geo-återställning."
services: sql-database
author: anosov1960
manager: jhubbard
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 03/05/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: e9ec0a0a602965561b77619123588db57c59993c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Återställa en Azure SQL Database eller växling vid fel till en sekundär
Azure SQL-databasen innehåller följande funktioner för återställning efter ett avbrott:

* [Aktiv geo-replikering och redundans grupper](sql-database-geo-replication-overview.md)
* [GEO-återställning](sql-database-recovery-using-backups.md#point-in-time-restore)
* [Zonredundant databaser](sql-database-high-availability.md)

Läs om business continuity scenarier och funktioner som stöder dessa scenarier i [företagskontinuitet](sql-database-business-continuity.md).

> [!NOTE]
> Om du använder zonredundant Premium-databaser och pooler återställningen sker automatiskt och resten av detta material är inte giltigt. 

### <a name="prepare-for-the-event-of-an-outage"></a>Förbereda för händelse av ett avbrott
För att lyckas med återställning till en annan dataområdet med redundans grupper eller geo-redundant säkerhetskopior måste du förbereda en server i en annan Datacenter avbrott blir den nya primära servern senare om behovet skulle uppstå samt ha väldefinierade stegen och testats för att säkerställa en smidig. Dessa förberedelsesteg inkluderar:

* Identifiera den logiska servern i en annan region ska bli den nya primära servern. För geo-återställning, detta är vanligtvis en server i den [parad region](../best-practices-availability-paired-regions.md) för den region där databasen är placerad. Detta eliminerar ytterligare trafik kostnaden under återställning av geo-åtgärder.
* Identifiera och du kan också definiera servernivå brandväggsregler som behövs för användare att få åtkomst till den nya primära databasen.
* Bestämma hur du kommer att omdirigera användare till den nya primära servern, exempelvis genom att ändra anslutningssträngar eller genom att ändra DNS-poster.
* Identifiera och om du vill skapa de inloggningar som måste finnas i master-databasen på den nya primära servern och se till att dessa inloggningar behörighet i master-databasen, om sådana finns. Mer information finns i [SQL Database-säkerhet efter återställning](sql-database-geo-replication-security-config.md)
* Identifiera Varningsregler som måste uppdateras för att mappa till den nya primära databasen.
* Dokumentet granskning konfigurationen på den aktuella primära databasen
* Utföra en [disaster recovery ökad](sql-database-disaster-recovery-drills.md). För att simulera ett avbrott för geo-återställning, kan du ta bort eller byta namn på källdatabasen om du vill att programmet anslutningsfel. Du kan inaktivera webbprogram eller virtuella datorn är ansluten till databasen eller failover databasen om du vill att anslutningen programfel för att simulera ett avbrott använda grupper för växling vid fel.

## <a name="when-to-initiate-recovery"></a>När du ska initiera återställningen
Återställningsåtgärden påverkar programmet. Det innebär att du ändrar SQL-anslutningssträngen eller med hjälp av DNS-omdirigering och kan resultera i permanent dataförlust. Det bör därför göras endast när avbrottet kan pågå längre än mål för ditt program. När programmet distribueras till produktion bör du utföra regelbundna övervakning av programmets hälsotillstånd och använder följande datapunkter för att bekräfta att återställningen garanteras:

1. Permanent anslutningsfel från programmet lager till databasen.
2. Azure portal visas en avisering om en incident i region med bred påverkan.

> [!NOTE]
> Om du använder grupper för växling vid fel och har valt automatisk redundans är återställningen automatiskt och transparent för programmet. 

Beroende på toleransvärde programmet driftstopp och möjliga ansvar för företag kan du överväga följande återställningsalternativ.

Använd den [hämta återställningsbara databasen](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) att hämta den senaste georeplikerad återställningspunkten.

## <a name="wait-for-service-recovery"></a>Vänta tills tjänsten återställning
Azure team fungerar ordentligt för att återställa tjänsttillgänglighet så snabbt som möjligt, men beroende på roten orsaka det kan ta timmar eller dagar.  Om ditt program kan tolerera betydande driftstopp kan du bara vänta för att slutföra återställningen. I så fall krävs ingen åtgärd. Du kan se aktuell status för tjänsten på vår [Azure Hälsoinstrumentpanelen](https://azure.microsoft.com/status/). Efter återställningen av region återställs programmets tillgänglighet.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Växla över till georeplikerad sekundär server i gruppen växling vid fel
Om ditt program driftstopp kan resultera i företag ansvar, bör du använda grupper för växling vid fel. Det gör att snabbt återställa tillgänglighet i en annan region vid ett strömavbrott. Lär dig hur du [konfigurera redundans grupper](sql-database-geo-replication-portal.md).

Om du vill återställa tillgängligheten för databaserna som du behöver initiera redundans till den sekundära servern med någon av metoderna som stöds.

Använd någon av följande guider för att växla över till en sekundär databas georeplikerad:

* [Växla över till en georeplikerad sekundär server med hjälp av Azure portal](sql-database-geo-replication-portal.md)
* [Växla över till den sekundära servern med hjälp av PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)

## <a name="recover-using-geo-restore"></a>Återställa med geo-återställning
Om ditt program driftstopp inte resulterar i business ansvar kan du använda [geo-återställning](sql-database-recovery-using-backups.md) som en metod för att återställa programmet-databaserna. Den skapar en kopia av databasen från den senaste geo-redundant säkerhetskopian.

## <a name="configure-your-database-after-recovery"></a>Konfigurera databasen efter återställningen
Om du använder geo-återställning för att återställa från ett avbrott, måste du kontrollera att anslutningen till de nya databaserna är korrekt konfigurerad så att funktionen normal program kan återupptas. Det här är en checklista för uppgifter för att förbereda din återställda databas produktion.

### <a name="update-connection-strings"></a>Uppdatera anslutningssträngar
Eftersom den återställda databasen finns i en annan server, måste du uppdatera ditt programs anslutningssträngen så att den pekar till servern.

Mer information om hur du ändrar anslutningssträngar finns i lämplig programmeringsspråk för din [anslutningsbibliotek](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Konfigurera brandväggsregler
Du måste se till att brandväggsreglerna som konfigurerats på servern och databasen matchar de som har konfigurerats på den primära servern och den primära databasen. Mer information finns i [så här: konfigurera brandväggsinställningar (Azure SQL Database)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Konfigurera inloggning och databasanvändare
Du måste se till att alla inloggningar som används av programmet finns på servern som är värd för den återställda databasen. Mer information finns i [säkerhetskonfiguration för geo-replikering](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Du bör konfigurera och testa din serverbrandväggsreglerna och inloggningar (och deras behörigheter) under en katastrofåterställning återställningsgranskning. Dessa servernivå objekt och deras konfiguration får inte vara tillgängliga under avbrottet.
> 
> 

### <a name="setup-telemetry-alerts"></a>Ställa in telemetri aviseringar
Du måste se till att de befintliga inställningarna för varningsregeln uppdateras för att mappa till den återställda databasen och annan server.

Mer information om databasen Varningsregler finns [ta emot aviseringen meddelanden](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) och [spåra tjänstens hälsa](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Aktivera granskning
Om granskning krävs för att ansluta till databasen, måste du aktivera granskning efter återställning av databasen. Mer information finns i [Database auditing](sql-database-auditing.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om Azure SQL Database automatiserad säkerhetskopieringar, se [SQL-databas automatisk säkerhetskopiering](sql-database-automated-backups.md)
* Läs om business continuity design- och återställningsscenarier i [kontinuitet scenarier](sql-database-business-continuity.md)
* Läs om hur du använder automatisk säkerhetskopiering för återställning i [återställa en databas från säkerhetskopior service-initierad](sql-database-recovery-using-backups.md)

