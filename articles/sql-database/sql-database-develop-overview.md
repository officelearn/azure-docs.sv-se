---
title: Översikt över SQL Database-programutveckling | Microsoft Docs
description: Läs mer om tillgängliga anslutningsbibliotek och bästa praxis för program som ansluter till SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 01c4bcfcea038f3e69620cdce78719c8c5128faf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964805"
---
# <a name="sql-database-application-development-overview"></a>Översikt över SQL Database-programutveckling

Den här artikeln beskriver grundläggande överväganden som utvecklare bör tänka på då de skriver kod för att ansluta till Azure SQL Database. Den här artikeln gäller för alla distributionsmodeller i Azure SQL Database (enkel databas, elastiska pooler, hanterad instans).

> [!TIP]
> Titta på komma igång-guider för [enkla databaser](sql-database-single-database-quickstart-guide.md) och [hanterade instanser](sql-database-managed-instance-quickstart-guide.md) om du vill konfigurera din Azure SQL Database.
>

## <a name="language-and-platform"></a>Språk och plattform

Du kan använda olika [programmeringsspråk och plattformar](sql-database-connect-query.md) att ansluta och fråga Azure SQL Database. Du kan hitta [programexempel](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) som du kan använda för att ansluta till Azure SQL-databasen.

Du kan använda verktyg för öppen källkod som [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/). Azure SQL Database fungerar dessutom med Microsoft-verktyg som [Visual Studio](https://www.visualstudio.com/downloads/) och [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Du kan också använda Azure portal, PowerShell och REST API: er hjälper dig att få ytterligare produktivitet.

## <a name="authentication"></a>Authentication

Åtkomst till Azure SQL Database är skyddad med inloggningar och brandväggar. Azure SQL Database stöder både SQL Server och [autentisering för Azure Active Directory (AAD)](sql-database-aad-authentication.md) användare och inloggningar. AAD-inloggningar är endast tillgängliga i Managed Instance. 

Läs mer om [hantera databasåtkomst och inloggning](sql-database-manage-logins.md).

## <a name="connections"></a>Anslutningar

I din klient för anslutningslogik åsidosätter du standardvärdet för timeout till att vara 30 sekunder. Standardvärdet på 15 sekunder är för kort för anslutningar som beror på internet.

Om du använder en [anslutningspool](https://msdn.microsoft.com/library/8xx3tyca.aspx), måste du stänga anslutningen så snart programmet inte aktivt använder den och inte förbereder sig för att återanvända den.

Undvik långvariga transaktioner eftersom underlåtenhet infrastruktur eller anslutningen kan Återställ transaktionen. Om det är möjligt, dela transaktion i flera mindre transaktioner och använda [batchbearbetning för att förbättra prestanda](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Återhämtning

Azure SQL Database är en molnbaserad tjänst där du kan förvänta dig att tillfälliga fel som inträffar i den underliggande infrastrukturen eller vid kommunikation mellan molnentiteter. Även om Azure SQL Database är flexibel på transitiv infrastrukturfel, kommer de här felen kan påverka anslutningen. När ett tillfälligt fel uppstår vid anslutning till SQL Database, bör din kod [göra om anropet](sql-database-connectivity-issues.md). Vi rekommenderar att logik för omprövning använder begränsningslogik så att den inte överbelastar SQL Database med flera klienter som försöker samtidigt. Logik för omprövning beror på den [felmeddelanden för SQL Database-klientprogram](sql-database-develop-error-messages.md).

Mer information om hur du förbereder planerat underhåll utförs på Azure SQL database finns i [planering för Azure-Underhåll i Azure SQL Database](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Nätverksöverväganden

- På den dator som är värd för ditt klientprogram, ska du se till att brandväggen tillåter utgående TCP-kommunikation på port 1433.  Mer information: [Konfigurera en Azure SQL Database-brandvägg](sql-database-configure-firewall-settings.md).
- Om klientprogrammet ansluter till SQL Database medan klienten körs på en Azure-dator (VM), måste du öppna vissa portintervall på den virtuella datorn. Mer information: [Portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Klientanslutningar till Azure SQL Database ibland kringgå proxyn och interagera direkt med databasen. Andra portar än 1433 blir viktiga. Mer information [Azure SQL Database anslutningsarkitektur](sql-database-connectivity-architecture.md) och [portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Nätverk configation för den hanterade instansen finns [nätverkskonfiguration för hanterade instanser](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Nästa steg

Utforska alla [funktionerna i SQL Database](sql-database-technical-overview.md).
