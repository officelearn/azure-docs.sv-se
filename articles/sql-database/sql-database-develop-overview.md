---
title: Översikt över programutveckling
description: Läs mer om tillgängliga anslutningsbibliotek och bästa praxis för program som ansluter till SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.openlocfilehash: 741906bbe9de68459b2e4a704a243fde4771b3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067331"
---
# <a name="sql-database-application-development-overview"></a>Översikt över utveckling av SQL Database-program

Den här artikeln beskriver grundläggande överväganden som utvecklare bör tänka på då de skriver kod för att ansluta till Azure SQL Database. Den här artikeln gäller alla distributionsmodeller av Azure SQL Database (Single database, Elastic pools, Managed instance).

> [!TIP]
> Titta på komma igång-guiderna för [enskilda databaser](sql-database-single-database-quickstart-guide.md) och [hanterade instanser](sql-database-managed-instance-quickstart-guide.md) om du behöver konfigurera din Azure SQL-databas.
>

## <a name="language-and-platform"></a>Språk och plattform

Du kan använda olika [programmeringsspråk och plattformar](sql-database-connect-query.md) för att ansluta och fråga Azure SQL Database. Du kan hitta [exempelprogram](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) som du kan använda för att ansluta till Azure SQL Database.

Du kan utnyttja verktyg med öppen källkod som [gepard](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS-kod](https://code.visualstudio.com/). Azure SQL Database fungerar dessutom med Microsoft-verktyg som [Visual Studio](https://www.visualstudio.com/downloads/) och [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Du kan också använda Azure-portalen, PowerShell och REST API:er som hjälper dig att få ytterligare produktivitet.

## <a name="authentication"></a>Autentisering

Åtkomst till Azure SQL Database är skyddad med inloggningar och brandväggar. Azure SQL Database stöder användare och inloggningar för både SQL Server och [Azure Active Directory (AAD).](sql-database-aad-authentication.md) AAD-inloggningar är endast tillgängliga i Hanterad instans. 

Läs mer om hur du [hanterar åtkomst till databaser och inloggning](sql-database-manage-logins.md).

## <a name="connections"></a>Anslutningar

I din klient för anslutningslogik åsidosätter du standardvärdet för timeout till att vara 30 sekunder. Standardvärdet på 15 sekunder är för kort för anslutningar som beror på internet.

Om du använder en [anslutningspool](https://msdn.microsoft.com/library/8xx3tyca.aspx), måste du stänga anslutningen så snart programmet inte aktivt använder den och inte förbereder sig för att återanvända den.

Undvik tidskrävande transaktioner eftersom alla infrastruktur- eller anslutningsfel kan återställa transaktionen. Om möjligt, dela upp transaktionen i flera mindre transaktioner och använda [batchbearbetning för att förbättra prestanda](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Återhämtning

Azure SQL Database är en molntjänst där du kan förvänta dig tillfälliga fel som inträffar i den underliggande infrastrukturen eller i kommunikationen mellan molnentiteter. Även om Azure SQL Database är flexibel på transitiva infrastrukturfel, kan dessa fel påverka din anslutning. När ett tillfälligt fel uppstår vid anslutning till SQL Database ska koden [försöka igen.](sql-database-connectivity-issues.md) Vi rekommenderar att logik för omprövning använder begränsningslogik så att den inte överbelastar SQL Database med flera klienter som försöker samtidigt. Logiken för återförsök beror på [felmeddelandena för SQL Database-klientprogram](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md).

Mer information om hur du förbereder dig för planerade underhållshändelser i din Azure SQL-databas finns [i planera för Azure-underhållshändelser i Azure SQL Database](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Nätverksöverväganden

- På den dator som är värd för ditt klientprogram, ska du se till att brandväggen tillåter utgående TCP-kommunikation på port 1433.  Mer information: [Konfigurera en Azure SQL Database-brandvägg](sql-database-configure-firewall-settings.md).
- Om klientprogrammet ansluter till SQL Database medan klienten körs på en virtuell Azure-dator (VM) måste du öppna vissa portintervall på den virtuella datorn. Mer information: [Portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Klientanslutningar till Azure SQL Database kringgår ibland proxyn och interagerar direkt med databasen. Andra portar än 1433 blir viktiga. Mer information finns [i Azure SQL Database-anslutningsarkitektur](sql-database-connectivity-architecture.md) [och portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- För nätverkskonfiguration för en hanterad instans finns i [nätverkskonfiguration för hanterade instanser](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Nästa steg

Utforska alla funktioner i [SQL Database](sql-database-technical-overview.md).
