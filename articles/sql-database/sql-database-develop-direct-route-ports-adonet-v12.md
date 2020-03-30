---
title: Portar bortom 1433
description: Klientanslutningar från ADO.NET till Azure SQL Database kan kringgå proxyn och interagera direkt med databasen med andra portar än 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 04/03/2019
ms.openlocfilehash: c0012b61cf43d01afd5e7f5f52948310b5eb8420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73828066"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portar utöver 1433 för ADO.NET 4.5

I det här avsnittet beskrivs azure SQL Database-anslutningsbeteendet för klienter som använder ADO.NET 4.5 eller en senare version.

> [!IMPORTANT]
> Information om anslutningsarkitektur finns i [Azure SQL Database-anslutningsarkitektur](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Utanför vs inuti

För anslutningar till Azure SQL Database måste vi först fråga om ditt klientprogram körs *utanför* eller *inom* Azure-molngränsen. Underavsnitten diskuterar två vanliga scenarier.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Utanför:* Klienten körs på din stationära dator

Port 1433 är den enda porten som måste vara öppen på din stationära dator som är värd för SQL Database-klientprogrammet.

### <a name="inside-client-runs-on-azure"></a>*Inuti:* Klienten körs på Azure

När klienten körs innanför Azure-molngränsen använder den vad vi kan kalla en *direkt väg* för att interagera med SQL Database-servern. När en anslutning har upprättats innebär ytterligare interaktioner mellan klienten och databasen ingen Azure SQL Database Gateway.

Sekvensen är följande:

1. ADO.NET 4.5 (eller senare) initierar en kort interaktion med Azure-molnet och tar emot ett dynamiskt identifierat portnummer.

   * Det dynamiskt identifierade portnumret ligger i intervallet 11000-11999.
2. ADO.NET ansluter sedan till SQL Database-servern direkt, utan mellanprogram däremellan.
3. Frågor skickas direkt till databasen och resultaten returneras direkt till klienten.

Kontrollera att portintervallen 11000-11999 på din Azure-klientdator lämnas tillgängliga för ADO.NET 4.5-klientinteraktioner med SQL Database.

* I synnerhet måste portarna i intervallet vara fria från andra utgående blockerare.
* På din virtuella Azure-brandvägg styr **Windows-brandväggen med avancerad säkerhet** portinställningarna.
  
  * Du kan använda [brandväggens användargränssnitt](https://msdn.microsoft.com/library/cc646023.aspx) för att lägga till en regel som du anger **TCP-protokollet** för tillsammans med ett portintervall med syntaxen som **11000-11999**.

## <a name="version-clarifications"></a>Versions förtydliganden

I det här avsnittet klargörs de monikers som refererar till produktversioner. Den listar också några parningar av versioner mellan produkter.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 stöder TDS 7.3-protokollet, men inte 7.4.
* ADO.NET 4.5 och senare stöder TDS 7.4-protokollet.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 eller senare

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 eller senare (JDBC 4.0 stöder faktiskt TDS 7.4 men implementerar inte "omdirigering")

## <a name="related-links"></a>Relaterade länkar

* ADO.NET 4.6 släpptes den 20 juli 2015. En blogg meddelande från .NET laget finns [här](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 släpptes den 15 augusti 2012. En blogg meddelande från .NET laget finns [här](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  * Ett blogginlägg om ADO.NET 4.5.1 finns [här](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx).

* Microsoft® ODBC Driver 17 för SQL Server® - Windows, Linux, & macOShttps://www.microsoft.com/download/details.aspx?id=56567

* Ansluta till Azure SQL Database V12 via omdirigeringhttps://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Versionslista för TDS-protokoll](https://www.freetds.org/userguide/tdshistory.htm)
* [Översikt över utveckling av SQL-databas](sql-database-develop-overview.md)
* [Brandvägg för Azure SQL Database](sql-database-firewall-configure.md)
* [Anvisningar: Konfigurera brandväggsinställningar på SQL Database](sql-database-configure-firewall-settings.md)


