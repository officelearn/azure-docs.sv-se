---
title: Portar utöver 1433 för SQL-databas | Microsoft Docs
description: Klientanslutningar från ADO.NET till Azure SQL-databas kan kringgå proxyn och interagera direkt med databasen med andra portar än 1433.
services: sql-database
author: MightyPen
manager: jhubbard
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: de40a45cfcc1b105384c6b5462511a372a546fc1
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portar utöver 1433 för ADO.NET 4.5
Det här avsnittet beskriver beteendet för Azure SQL Database-anslutningen för klienter som använder ADO.NET 4.5 eller senare. 

> [!IMPORTANT]
> Information om anslutningen arkitektur finns [Azure SQL Database connectivity arkitektur](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Utanför eller innanför
För anslutningar till Azure SQL-databas måste först ber vi om klientprogrammet körs *utanför* eller *inuti* gräns för Azure-molnet. I underavsnitt beskrivs två vanliga scenarier.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Utanför:* klienten körs på datorn
Port 1433 är den enda port som måste vara öppna på den stationära datorn som är värd för ditt SQL Database-klientprogram.

#### <a name="inside-client-runs-on-azure"></a>*Inre:* klienten körs på Azure
När klienten körs inuti Azure-molnet, används vad vi kan anropa en *vägen* att interagera med SQL Database-servern. När en anslutning har upprättats ytterligare omfattar samverkan mellan klienten och databasen ingen Azure SQL Database-Gateway.

Sekvensen är följande:

1. ADO.NET 4.5 (eller senare) initierar en kort interaktion med Azure-molnet och tar emot ett dynamiskt identifierade portnummer.
   
   * Dynamiskt identifierade portnumret är i intervallet 11000 11999 eller 14000 14999.
2. ADO.NET sedan ansluter till SQL Database-server direkt, utan mellanprogram mellan.
3. Frågor skickas direkt till databasen och resultat returneras direkt till klienten.

Se till att den port som intervall med 11000 11999 och 14000 14999 på Azure klientdatorn lämnas tillgänglig för ADO.NET 4.5 klienten interaktioner med SQL-databas.

* I synnerhet måste portar i intervallet vara fria från andra utgående blockeringar.
* På den virtuella datorn i Azure, den **Windows-brandväggen med avancerad säkerhet** styr vilka portinställningar.
  
  * Du kan använda den [brandväggens användargränssnittet](http://msdn.microsoft.com/library/cc646023.aspx) att lägga till en regel som du anger den **TCP** protokollet tillsammans med ett portintervall med syntax som **11000 11999**.

## <a name="version-clarifications"></a>Version förtydliganden
Det här avsnittet förklarar monikrar som refererar till produktversioner. Dessutom visas vissa pairings versioner mellan produkter.

#### <a name="adonet"></a>ADO.NET
* ADO.NET 4.0 stöder 7.3 TDS-protokollet, men inte 7.4.
* ADO.NET 4.5 och senare stöder 7.4 TDS-protokollet.

## <a name="related-links"></a>Relaterade länkar
* ADO.NET 4.6 gavs ut 20 juli 2015. En blogg meddelande från .NET-teamet finns [här](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 gavs ut 15 augusti 2012. En blogg meddelande från .NET-teamet finns [här](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  
  * Det finns ett blogginlägg om ADO.NET 4.5.1 [här](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).
* [TDS-protokollet versionslista](http://www.freetds.org/userguide/tdshistory.htm)
* [Översikt över SQL Database-utveckling](sql-database-develop-overview.md)
* [Azure SQL Database-brandvägg](sql-database-firewall-configure.md)
* [Så här: konfigurera brandväggsinställningar på SQL-databas](sql-database-configure-firewall-settings.md)

