---
title: Portar utöver 1433 för SQL-databas | Microsoft Docs
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
manager: craigg
ms.date: 04/03/2019
ms.openlocfilehash: ddb115370c62371e769ef98e0031f7e0379bafbf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916180"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portar utöver 1433 för ADO.NET 4.5

Det här avsnittet beskriver beteendet för Azure SQL Database-anslutning för klienter som använder ADO.NET 4.5 eller senare.

> [!IMPORTANT]
> Läs om hur anslutningsarkitektur [Azure SQL Database anslutningsarkitektur](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Utanför eller inuti

För anslutningar till Azure SQL Database, måste vi först be om klientprogrammet körs *utanför* eller *inuti* gränsen för Azure-molnet. I underavsnitt beskrivs två vanliga scenarier.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Utomhus:* Klienten kör på din dator

Port 1433 är den enda port som måste vara öppna på din dator som är värd för din SQL Database-klientprogram.

### <a name="inside-client-runs-on-azure"></a>*Inuti:* Klienten kör på Azure

När klienten körs inom gränsen för Azure-molnet, använder vi kan anropa en *vägen* att interagera med SQL Database-servern. När en anslutning har upprättats kan ytterligare omfattar samverkan mellan klienten och databasen inga Azure SQL Database-Gateway.

Sekvensen är följande:

1. ADO.NET 4.5 (eller senare) initierar en kort interaktion med Azure-molnet och tar emot ett dynamiskt identifierade portnummer.

   * Dynamiskt identifierade portnumret är i intervallet 11000 11999.
2. ADO.NET ansluter sedan till SQL Database-server direkt, med inga mellanprogram däremellan.
3. Frågor skickas direkt till databasen och resultaten returneras direkt till klienten.

Kontrollera att port intervallen för 11000-11999 på Azure klientdatorn lämnas tillgänglig för ADO.NET 4.5 klienten interaktioner med SQL-databas.

* I synnerhet måste portar i intervallet vara fria från andra utgående blockeringar.
* På din Azure-dator, den **Windows-brandväggen med avancerad säkerhet** styr vilka portinställningar som.
  
  * Du kan använda den [brandväggens gränssnitt](https://msdn.microsoft.com/library/cc646023.aspx) att lägga till en regel som du anger den **TCP** protokollet tillsammans med ett portintervall med syntax som **11000 11999**.

## <a name="version-clarifications"></a>Version förtydliganden

Det här avsnittet visar monikers som refererar till produktversioner. Den visar även vissa par mellan produkter.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 stöder 7.3 TDS-protokollet, men inte 7.4.
* ADO.NET 4.5 och senare stöder 7.4 TDS-protokollet.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 eller senare

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 eller senare (JDBC 4.0 faktiskt stöder TDS 7.4 men implementerar inte ”omdirigering”)

## <a name="related-links"></a>Relaterade länkar

* ADO.NET 4.6 gavs ut den 20 juli 2015. En bloggmeddelandet från .NET-teamet är tillgängligt [här](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 gavs ut den 15 augusti 2012. En bloggmeddelandet från .NET-teamet är tillgängligt [här](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  * Det finns ett blogginlägg om ADO.NET 4.5.1 [här](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx).

* Microsoft® ODBC Driver 17 för SQL Server® – Windows, Linux och macOS https://www.microsoft.com/download/details.aspx?id=56567

* Ansluta till Azure SQL Database V12 via omdirigering https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [TDS-protokollversionslistan](http://www.freetds.org/userguide/tdshistory.htm)
* [Översikt över SQL Database-utveckling](sql-database-develop-overview.md)
* [Azure SQL Database-brandvägg](sql-database-firewall-configure.md)
* [Anvisningar: Konfigurera brandväggsinställningar för SQL Database](sql-database-configure-firewall-settings.md)


