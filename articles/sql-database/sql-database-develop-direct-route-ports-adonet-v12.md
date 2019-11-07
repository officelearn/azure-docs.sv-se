---
title: Portar utöver 1433 för SQL Database
description: Klient anslutningar från ADO.NET till Azure SQL Database kan kringgå proxyn och interagera direkt med databasen med andra portar än 1433.
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
ms.openlocfilehash: 38ab3b6b0c64ad17311d15fd12e7625e8aa728ce
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690681"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portar utöver 1433 för ADO.NET 4,5

I det här avsnittet beskrivs Azure SQL Database anslutnings beteendet för klienter som använder ADO.NET 4,5 eller en senare version.

> [!IMPORTANT]
> Information om anslutnings arkitektur finns i [Azure SQL Database anslutnings arkitektur](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Utanför vs inuti

För att anslutningar ska kunna Azure SQL Database måste vi först fråga om klient programmet körs *utanför* eller *innanför* molnets gränser i Azure. Underavsnitten diskuterar två vanliga scenarier.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Utanför:* Klienten körs på din station ära dator

Port 1433 är den enda port som måste vara öppen på den station ära datorn som är värd för SQL Database klient programmet.

### <a name="inside-client-runs-on-azure"></a>*Inuti:* Klienten körs på Azure

När klienten körs inom Azure-molnets gränser, använder den vad vi kan anropa en *direkt väg* för att interagera med den SQL Database servern. När en anslutning har upprättats omfattar ytterligare interaktioner mellan klienten och databasen ingen Azure SQL Database Gateway.

Ordningen är följande:

1. ADO.NET 4,5 (eller senare) initierar en kort interaktion med Azure-molnet och tar emot ett dynamiskt identifierat port nummer.

   * Det dynamiskt identifierade port numret är inom intervallet 11000-11999.
2. ADO.NET ansluter sedan till SQL Database-servern direkt, utan mellan.
3. Frågor skickas direkt till databasen och resultaten returneras direkt till klienten.

Se till att port intervallen för 11000-11999 på Azure-klientdatorn lämnas tillgängliga för ADO.NET 4,5-klienter med SQL Database.

* I synnerhet måste portarna i intervallet vara fria från andra utgående blockerare.
* På din virtuella Azure-dator kontrollerar **Windows-brandväggen med avancerad säkerhet** port inställningarna.
  
  * Du kan använda [brand väggens användar gränssnitt](https://msdn.microsoft.com/library/cc646023.aspx) för att lägga till en regel för vilken du anger **TCP** -protokollet tillsammans med ett port intervall med syntaxen som **11000-11999**.

## <a name="version-clarifications"></a>Versions klargöranden

I det här avsnittet klargöras de monikers som refererar till produkt versioner. Här visas även några par olika versioner mellan produkter.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4,0 stöder TDS 7,3-protokollet, men inte 7,4.
* ADO.NET 4,5 och senare har stöd för TDS 7,4-protokollet.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 eller senare

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4,2 eller senare (JDBC 4,0 faktiskt stöder TDS 7,4 men implementerar inte "omdirigering")

## <a name="related-links"></a>Relaterade länkar

* ADO.NET 4,6 gavs ut den 20 juli 2015. Ett blogg meddelande från .NET-teamet finns [här](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx).
* ADO.NET 4,5 släpptes den 15 augusti 2012. Ett blogg meddelande från .NET-teamet finns [här](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  * Ett blogg inlägg om ADO.NET 4.5.1 finns [här](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx).

* Microsoft® ODBC driv rutin 17 för SQL Server® – Windows, Linux, & macOS https://www.microsoft.com/download/details.aspx?id=56567

* Anslut till Azure SQL Database V12 via omdirigering https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lista över TDS-protokoll](https://www.freetds.org/userguide/tdshistory.htm)
* [Översikt över SQL Database utveckling](sql-database-develop-overview.md)
* [Azure SQL Database brand vägg](sql-database-firewall-configure.md)
* [Gör så här: Konfigurera brand Väggs inställningar på SQL Database](sql-database-configure-firewall-settings.md)


