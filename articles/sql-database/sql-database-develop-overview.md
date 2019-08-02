---
title: Översikt över SQL Database-programutveckling | Microsoft Docs
description: Läs mer om tillgängliga anslutningsbibliotek och bästa praxis för program som ansluter till SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 02/07/2019
ms.openlocfilehash: 42fc73b5557fba91cc132a0abe8561f0a72bbb64
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568861"
---
# <a name="sql-database-application-development-overview"></a>Översikt över SQL Database program utveckling

Den här artikeln beskriver grundläggande överväganden som utvecklare bör tänka på då de skriver kod för att ansluta till Azure SQL Database. Den här artikeln gäller alla distributions modeller för Azure SQL Database (enkel databas, elastiska pooler, hanterade instanser).

> [!TIP]
> Titta på komma igång-guiderna för [enskilda databaser](sql-database-single-database-quickstart-guide.md) och [hanterade instanser](sql-database-managed-instance-quickstart-guide.md) om du behöver konfigurera din Azure SQL Database.
>

## <a name="language-and-platform"></a>Språk och plattform

Du kan använda olika [programmeringsspråk och plattformar](sql-database-connect-query.md) för att ansluta och fråga Azure SQL Database. Du kan hitta [exempel program](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) som du kan använda för att ansluta till Azure SQL Database.

Du kan utnyttja verktyg med öppen källkod som [Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli), [vs Code](https://code.visualstudio.com/). Azure SQL Database fungerar dessutom med Microsoft-verktyg som [Visual Studio](https://www.visualstudio.com/downloads/) och [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Du kan också använda API: erna Azure Portal, PowerShell och REST för att få ytterligare produktivitet.

## <a name="authentication"></a>Authentication

Åtkomst till Azure SQL Database skyddas med inloggningar och brand väggar. Azure SQL Database stöder användare och inloggningar både SQL Server och [Azure Active Directory (AAD)-autentisering](sql-database-aad-authentication.md) . AAD-inloggningar är bara tillgängliga i hanterade instanser. 

Lär dig mer om [att hantera databas åtkomst och inloggning](sql-database-manage-logins.md).

## <a name="connections"></a>Anslutningar

I din klient för anslutningslogik åsidosätter du standardvärdet för timeout till att vara 30 sekunder. Standardvärdet på 15 sekunder är för kort för anslutningar som beror på internet.

Om du använder en [anslutningspool](https://msdn.microsoft.com/library/8xx3tyca.aspx), måste du stänga anslutningen så snart programmet inte aktivt använder den och inte förbereder sig för att återanvända den.

Undvik tids krävande transaktioner eftersom en infrastruktur eller ett anslutnings haveri kan återställa transaktionen. Om möjligt kan du dela upp transaktionen i flera mindre transaktioner och använda [batching för att förbättra prestanda](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Återhämtning

Azure SQL Database är en moln tjänst där du kan vänta på tillfälliga fel som inträffar i den underliggande infrastrukturen eller i kommunikationen mellan moln enheter. Även om Azure SQL Database är elastisk på de transitiva infrastruktur felen kan dessa problem påverka din anslutning. När ett tillfälligt fel uppstår när du ansluter till SQL Database, ska koden [försöka anropa igen](sql-database-connectivity-issues.md). Vi rekommenderar att logik för omprövning använder begränsningslogik så att den inte överbelastar SQL Database med flera klienter som försöker samtidigt. Omprövnings logik är beroende av [fel meddelanden för SQL Database klient program](sql-database-develop-error-messages.md).

Mer information om hur du förbereder för planerade underhålls händelser på din Azure SQL-databas finns i [Planera för underhålls händelser i Azure i Azure SQL Database](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Nätverks överväganden

- På den dator som är värd för ditt klientprogram, ska du se till att brandväggen tillåter utgående TCP-kommunikation på port 1433.  Mer information: [Konfigurera en Azure SQL Database brand vägg](sql-database-configure-firewall-settings.md).
- Om klient programmet ansluter till SQL Database medan klienten körs på en virtuell dator i Azure måste du öppna vissa port intervall på den virtuella datorn. Mer information: [Portar utöver 1433 för ADO.NET 4,5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Klient anslutningar till Azure SQL Database ibland kringgå proxyn och interagera direkt med databasen. Andra portar än 1433 blir viktiga. Mer information [Azure SQL Database anslutnings arkitektur](sql-database-connectivity-architecture.md) och [portar utöver 1433 för ADO.NET 4,5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Nätverks konfiguration för en hanterad instans finns i [nätverks konfiguration för hanterade instanser](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Nästa steg

Utforska alla [funktionerna i SQL Database](sql-database-technical-overview.md).
