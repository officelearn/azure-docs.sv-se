---
title: "Översikt över SQL Database-programutveckling | Microsoft Docs"
description: "Läs mer om tillgängliga anslutningsbibliotek och bästa praxis för program som ansluter till SQL Database."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: genemi
ms.assetid: 67c02204-d1bd-4622-acce-92115a7cde03
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 72a20c51416cd3022e2a749c51f25c4b3d9268df
ms.openlocfilehash: 88b8076e740f80154e410050cd55cd2fa4783b38


---
# <a name="sql-database-application-development-overview"></a>Översikt över SQL Database-programutveckling
Den här artikeln beskriver grundläggande överväganden som utvecklare bör tänka på då de skriver kod för att ansluta till Azure SQL Database.

> [!TIP]
> En genomgång som visar dig hur du skapar en server, skapar en serverbaserad brandvägg, visar egenskaper, ansluter med SQL Server Management Studio, frågar huvuddatabasen, skapar en exempeldatabas och en tom databas, frågar om databasegenskaper, ansluter med SQL Server Management Studio och frågar exempeldatabasen, finns i[	Komma igång - Självstudier](sql-database-get-started.md).
>

## <a name="language-and-platform"></a>Språk och plattform
Det finns kodexempel för olika programmeringsspråk och plattformar. Du hittar länkar till kodexemplen på: 

* Mer information: [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)

## <a name="resource-limitations"></a>Resursbegränsningar
Azure SQL Database hanterar de resurser som är tillgängliga för en databas på två olika sätt: resursstyrning och verkställandet av gränser.

* Mer information: [Resursgränser för Azure SQL Database](sql-database-resource-limits.md)

## <a name="security"></a>Säkerhet
Azure SQL Database innehåller resurser för att begränsa åtkomst, skydda data och övervaka aktiviteter på en SQL Database.

* Mer information: [Säkra din SQL Database](sql-database-security.md)

## <a name="authentication"></a>Autentisering
* Azure SQL Database stöder både användare och inloggningar för SQL Server-autentisering samt användare och inloggningar för [Azure Active Directory-autentisering](sql-database-aad-authentication.md).
* Du måste ange en viss databas i stället för *huvud*databasen som standard.
* Du kan inte använda uttrycket Transact-SQL **USE myDatabaseName;** i SQL Database för att växla till en annan databas.
* Mer information: [SQL Database-säkerhet: hantera databasåtkomst och inloggningssäkerhet](sql-database-manage-logins.md)

## <a name="resiliency"></a>Återhämtning
När ett tillfälligt fel uppstår vid anslutning till SQL Database, bör din kod göra om anropet.  Vi rekommenderar att logik för omprövning använder begränsningslogik så att den inte överbelastar SQL Database med flera klienter som försöker samtidigt.

* Kodexempel: För kodexempel som illustrerar logik för omprövning, se exempel för önskat språk på: [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)
* Mer information: [Felmeddelanden för SQL Database-klientprogram](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Hantera anslutningar
* I din klient för anslutningslogik åsidosätter du standardvärdet för timeout till att vara 30 sekunder.  Standardvärdet på 15 sekunder är för kort för anslutningar som beror på internet.
* Om du använder en [anslutningspool](http://msdn.microsoft.com/library/8xx3tyca.aspx), måste du stänga anslutningen så snart programmet inte aktivt använder den och inte förbereder sig för att återanvända den.

## <a name="network-considerations"></a>Nätverksöverväganden
* På den dator som är värd för ditt klientprogram, ska du se till att brandväggen tillåter utgående TCP-kommunikation på port 1433.  Mer information: [Konfigurera en Azure SQL Database-brandvägg](sql-database-configure-firewall-settings.md)
* Om klientprogrammet ansluter till SQL Database V12 medan klienten körs på en virtuell Azure-dator (VM), måste du öppna vissa portintervall på den virtuella datorn. Mer information: [Portar utanför 1433 för ADO.NET 4.5 och SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)
* Klientanslutningar till Azure SQL Database V12 kringgår ibland proxyservern och interagerar direkt med databasen. Andra portar än 1433 blir viktiga. Mer information: [Portar utanför 1433 för ADO.NET 4.5 och SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Horisontell datapartitionering med elastisk skalning
Elastisk skalning gör det enklare att skala ut (och in). 

* [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md)
* [Kom igång med förhandsversionen av Elastic Scale för Azure SQL Database](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Nästa steg
Utforska alla [funktionerna i SQL Database](https://azure.microsoft.com/services/sql-database/).




<!--HONumber=Dec16_HO3-->


