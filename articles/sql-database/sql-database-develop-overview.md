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
manager: craigg
ms.date: 06/20/2018
ms.openlocfilehash: 707e10f77bf00ed12f09a23e490105f52ceed4ab
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241607"
---
# <a name="sql-database-application-development-overview"></a>Översikt över SQL Database-programutveckling
Den här artikeln beskriver grundläggande överväganden som utvecklare bör tänka på då de skriver kod för att ansluta till Azure SQL Database.

> [!TIP]
> En självstudiekurs som visar hur du skapar en server, skapar en serverbaserad brandvägg, visa egenskaperna för servern, ansluta med hjälp av SQL Server Management Studio, frågar huvuddatabasen, skapa en exempeldatabas och en tom databas, frågar om Databasegenskaper, ansluter med SQL Server Management Studio och fråga exempeldatabasen, se [komma igång-Självstudier](sql-database-get-started-portal.md).
>

## <a name="language-and-platform"></a>Språk och plattform
Det finns kodexempel för olika programmeringsspråk och plattformar. Du hittar länkar till kodexemplen på: 

* Mer information: [anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md).

## <a name="tools"></a>Verktyg 
Du kan använda verktyg för öppen källkod som [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/). Azure SQL Database fungerar dessutom med Microsoft-verktyg som [Visual Studio](https://www.visualstudio.com/downloads/) och [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).  Du kan också använda Azure-hanteringsportalen, PowerShell och REST API: er för att ytterligare höja produktiviteten.

## <a name="resource-limitations"></a>Resursbegränsningar
Azure SQL Database hanterar resurserna som är tillgängliga för en databas på två olika sätt: resursstyrning och verkställandet av gränser. Mer information finns i:

- [DTU-baserade modellen resursbegränsningar - databas](sql-database-dtu-resource-limits-single-databases.md)
- [DTU-baserade modellen resursbegränsningar - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)
- [vCore-baserade resursbegränsningar - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)
- [vCore-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)

## <a name="security"></a>Säkerhet
Azure SQL Database innehåller resurser för att begränsa åtkomst, skydda data och övervaka aktiviteter på en SQL Database.

* Mer information: [säkra din SQL Database](sql-database-security-overview.md).

## <a name="authentication"></a>Autentisering
* Azure SQL Database stöder både användare och inloggningar för SQL Server-autentisering samt användare och inloggningar för [Azure Active Directory-autentisering](sql-database-aad-authentication.md).
* Du måste ange en viss databas i stället för *huvud*databasen som standard.
* Du kan inte använda uttrycket Transact-SQL **USE myDatabaseName;** i SQL Database för att växla till en annan databas.
* Mer information: [SQL Database-säkerhet: hantera åtkomst och logga in databassäkerhet](sql-database-manage-logins.md).

## <a name="resiliency"></a>Återhämtning
När ett tillfälligt fel uppstår vid anslutning till SQL Database, bör din kod göra om anropet.  Vi rekommenderar att logik för omprövning använder begränsningslogik så att den inte överbelastar SQL Database med flera klienter som försöker samtidigt.

* Kodexempel: för kodexempel som illustrerar logik för omprövning, se exempel för valfritt på språk: [anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md).
* Mer information: [felmeddelanden för SQL Database-klientprogram](sql-database-develop-error-messages.md).

## <a name="managing-connections"></a>Hantera anslutningar
* I din klient för anslutningslogik åsidosätter du standardvärdet för timeout till att vara 30 sekunder.  Standardvärdet på 15 sekunder är för kort för anslutningar som beror på internet.
* Om du använder en [anslutningspool](https://msdn.microsoft.com/library/8xx3tyca.aspx), måste du stänga anslutningen så snart programmet inte aktivt använder den och inte förbereder sig för att återanvända den.

## <a name="network-considerations"></a>Nätverksöverväganden
* På den dator som är värd för ditt klientprogram, ska du se till att brandväggen tillåter utgående TCP-kommunikation på port 1433.  Mer information: [konfigurera en Azure SQL Database-brandvägg](sql-database-configure-firewall-settings.md).
* Om klientprogrammet ansluter till SQL Database medan klienten körs på en Azure-dator (VM), måste du öppna vissa portintervall på den virtuella datorn. Mer information: [portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
* Klientanslutningar till Azure SQL Database ibland kringgå proxyn och interagera direkt med databasen. Andra portar än 1433 blir viktiga. Mer information [Azure SQL Database anslutningsarkitektur](sql-database-connectivity-architecture.md) och [portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="data-sharding-with-elastic-scale"></a>Horisontell Datapartitionering med elastisk skalning
Elastisk skalning gör enklare att skala ut (och in). 

* [Designmönster för flera innehavare SaaS-program med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md).
* [Kom igång med förhandsversionen av Azure SQL Database Elastic Scale](sql-database-elastic-scale-get-started.md).

## <a name="next-steps"></a>Nästa steg
Utforska alla [funktionerna i SQL Database](sql-database-technical-overview.md).
