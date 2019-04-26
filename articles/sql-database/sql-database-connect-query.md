---
title: Snabbstarter för Azure SQL Database-anslutning och fråga | Microsoft Docs
description: Snabbstarterna för Azure SQL Database visar hur du ansluter till och frågar en Azure SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 1bf9c0b1393abfcad6646624301c4f131c8790a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320949"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Snabbstarter: Azure SQL Database-anslutning och fråga

Följande dokument innehåller länkar till Azure-exempel som beskriver hur du ansluter till och frågar en Azure SQL-databas. Det innehåller också några rekommendationer för TLS (Transport Level Security).

## <a name="quickstarts"></a>Snabbstarter

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Den här snabbstarten visar hur du använder SSMS för att ansluta till en Azure SQL-databas och sedan använder Transact-SQL-uttryck för att skicka frågor mot, infoga, uppdatera och ta bort data i databasen.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Den här snabbstarten visar hur du använder Azure Data Studio för att ansluta till en Azure SQL-databas och sedan använder Transact-SQL-instruktioner (T-SQL) till att skapa den TutorialDB som används i självstudierna för Azure Data Studio.|
|[Azure Portal](sql-database-connect-query-portal.md)|Den här snabbstarten visar hur du ansluter till en SQL-databas med frågeredigeraren och sedan använder Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Den här snabbstarten visar hur du använder Visual Studio Code för att ansluta till en Azure SQL-databas och sedan använder Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen.|
|[.NET med Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Den här snabbstarten visar hur du använder .NET Framework till att skapa ett C#-program med Visual Studio för anslutning till en Azure SQL-databas och använder Transact-SQL-instruktioner till att köra frågor mot data.|
|[.NET core](sql-database-connect-query-dotnet-core.md)|Den här snabbstarten beskriver hur du använder .NET Core i Windows/Linux/Mac OS för att skapa ett C#-program som ansluter till en Azure SQL-databas och använder Transact-SQL-instruktioner till att köra frågor mot data.|
|[Go](sql-database-connect-query-go.md)|Den här snabbstarten visar hur du använder Go för att ansluta till en Azure SQL-databas. Här visas också hur du använder Transact-SQL-uttryck för att köra frågor mot och ändra data.|
|[Java](sql-database-connect-query-java.md)|Den här snabbstarten visar hur du använder Java för att ansluta till en Azure SQL-databas och sedan använder Transact-SQL-instruktioner för att köra frågor mot data.|
|[Node.js](sql-database-connect-query-nodejs.md)|Den här snabbstarten visar hur du använder Node.js för att skapa ett program som ansluter till en Azure SQL-databas och använder Transact-SQL-instruktioner för att köra frågor mot data.|
|[PHP](sql-database-connect-query-php.md)|Den här snabbstarten visar hur du använder PHP för att skapa ett program som ansluter till en Azure SQL-databas och använder Transact-SQL-instruktioner för att köra frågor mot data.|
|[Python](sql-database-connect-query-python.md)|Den här snabbstarten visar hur du använder Python för att ansluta till en Azure SQL-databas och hur du använder Transact-SQL-instruktioner för att köra frågor mot data. |
|[Ruby](sql-database-connect-query-ruby.md)|Den här snabbstarten visar hur du använder Ruby för att skapa ett program som ansluter till en Azure SQL-databas och använder Transact-SQL-instruktioner för att köra frågor mot data.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>TLS-överväganden för SQL Database-anslutning
TLS (Transport Layer Security) används av alla drivrutiner som Microsoft tillhandahåller eller stöder för anslutning till Azure SQL Database. Ingen särskild konfiguration krävs. För alla anslutningar till SQL Server eller till Azure SQL Database rekommenderar vi att alla program har följande eller likvärdiga konfigurationer:

 - **Kryptera = På**
 - **TrustServerCertificate = Av**

Vissa system använder andra men likvärdiga nyckelord för dessa konfigurationsnyckelord. Dessa konfigurationer ser till att klientdrivrutinen bekräftar identiteten för TLS-certifikatet som togs emot från servern.

Vi rekommenderar också att du inaktiverar TLS 1.1 och 1.0 på klienten om du behöver följa datasäkerhetsstandarden PCI-DSS (Payment Card Industry – Data Security Standard).

Drivrutiner som inte kommer från Microsoft kanske inte använder TLS som standard. Detta är viktigt att ha i åtanke vid anslutning till Azure SQL Database. Du kanske inte kan ändra dessa anslutningsinställningar i program med inbäddade drivrutiner. Vi rekommenderar att du kontrollerar säkerheten i sådana drivrutiner och program innan du använder dem i system som hanterar känsliga data.

## <a name="libraries"></a>Bibliotek

Du kan använda olika bibliotek och ramverk för att ansluta till Azure SQL Database. Kolla in våra [inledande självstudier](https://aka.ms/sqldev) om du snabbt vill komma igång med programmeringsspråk som C#, Java, Node.js, PHP och Python. Skapa sedan en app med hjälp av SQL Server i Linux eller Windows, eller Docker i macOS.

I följande tabell ser du vilka anslutningsbibliotek eller *drivrutiner* som klientprogram kan använda i olika språk för att ansluta till och använda SQL Server lokalt eller i molnet. Du kan använda dem i Linux, Windows och Docker när du vill ansluta till Azure SQL Database eller Azure SQL Data Warehouse. 

| Språk | Plattform | Ytterligare resurser | Ladda ned | Kom igång |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET för SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Ladda ned](https://www.microsoft.com/net/download/) | [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Ladda ned](https://go.microsoft.com/fwlink/?linkid=852460) |  [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-drivrutin för SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Ladda ned](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Installera](https://msdn.microsoft.com/library/mt652094.aspx) |  [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-drivrutin](https://msdn.microsoft.com/library/mt652092.aspx) | Installationsval: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Installera](https://msdn.microsoft.com/library/mt711041.aspx) | [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Ladda ned](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

I följande tabell visas exempel på ORM-ramverk (objektrelationell mappning) och webbramverk som klientprogram kan använda när SQL Server körs lokalt eller i molnet. Du kan använda ramverken i Linux, Windows och Docker när du vill ansluta till SQL Database eller SQL Data Warehouse. 

| Språk | Plattform | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Nästa steg

- Information om anslutningsarkitekturen finns i [Azure SQL Database Connectivity Architecture](sql-database-connectivity-architecture.md) (Anslutningsarkitektur för Azure SQL Database).
- Hitta [SQL Server-drivrutiner](https://msdn.microsoft.com/library/mt654049.aspx) som används till att ansluta från klientprogram
- Ansluta till SQL Database:
  - [Ansluta till SQL Database med .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Ansluta till SQL Database med PHP](sql-database-connect-query-php.md) 
  - [Ansluta till SQL Database med Node.js](sql-database-connect-query-nodejs.md) 
  - [Ansluta till SQL Database med Java](sql-database-connect-query-java.md) 
  - [Anslut till SQL Database med Python](sql-database-connect-query-python.md)
  - [Ansluta till SQL Database med Ruby](sql-database-connect-query-ruby.md)
- Kodexempel för omprövningslogik:
  - [Ansluta elastiskt till SQL med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Ansluta elastiskt till SQL med PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
