---
title: Anslut och fråga innehålls referens
description: En referens till Azure SQL Database snabb starter som visar hur du ansluter till och frågar Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 12e53e6960cbd55b78c8459ae9db080f32e2ffb6
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84189641"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Snabb starter: Azure SQL Database Anslut och fråga
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Följande dokument innehåller länkar till Azure-exempel som visar hur du ansluter och frågar Azure SQL Database. Det innehåller också några rekommendationer för TLS (Transport Level Security).

## <a name="quickstarts"></a>Snabbstarter

| |  |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|Den här snabb starten visar hur du använder SSMS för att ansluta till en databas i Azure SQL Database och sedan använda Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Den här snabb starten visar hur du använder Azure Data Studio för att ansluta till en databas i Azure SQL Database och sedan använda Transact-SQL-uttryck (T-SQL) för att skapa den TutorialDB som används i Azure Data Studio själv studie kurser.|
|[Azure Portal](connect-query-portal.md)|Den här snabb starten visar hur du använder Frågeredigeraren för att ansluta till en databas i Azure SQL Database och sedan använda Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen.|
|[Visual Studio-koden](connect-query-vscode.md)|Den här snabb starten visar hur du använder Visual Studio Code för att ansluta till en databas i Azure SQL Database och sedan använda Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen.|
|[.NET med Visual Studio](connect-query-dotnet-visual-studio.md)|Den här snabb starten visar hur du använder .NET Framework för att skapa ett C#-program med Visual Studio för att ansluta till en databas i Azure SQL Database och använda Transact-SQL-uttryck för att fråga data.|
|[.NET Core](connect-query-dotnet-core.md)|Den här snabb starten visar hur du använder .NET Core på Windows/Linux/macOS för att skapa ett C#-program för att ansluta till en databas i Azure SQL Database och använda Transact-SQL-uttryck för att fråga data.|
|[Kör](connect-query-go.md)|Den här snabb starten visar hur du använder Go för att ansluta till en databas i Azure SQL Database. Här visas också hur du använder Transact-SQL-uttryck för att köra frågor mot och ändra data.|
|[Java](connect-query-java.md)|Den här snabb starten visar hur du använder Java för att ansluta till en databas i Azure SQL Database och sedan använder Transact-SQL-uttryck för att fråga data.|
|[Node.js](connect-query-nodejs.md)|Den här snabb starten visar hur du använder Node. js för att skapa ett program för att ansluta till en databas i Azure SQL Database och använda Transact-SQL-uttryck för att fråga data.|
|[PHP](connect-query-php.md)|Den här snabb starten visar hur du använder PHP för att skapa ett program för att ansluta till en databas i Azure SQL Database och använda Transact-SQL-uttryck för att fråga data.|
|[Python](connect-query-python.md)|Den här snabb starten visar hur du använder python för att ansluta till en databas i Azure SQL Database och använder Transact-SQL-uttryck för att fråga data. |
|[Ruby](connect-query-ruby.md)|Den här snabb starten visar hur du använder ruby för att skapa ett program för att ansluta till en databas i Azure SQL Database och använda Transact-SQL-uttryck för att fråga data.|
|[R](connect-query-r.md)|Den här snabb starten visar hur du använder R med Azure SQL Database Machine Learning Services för att skapa ett program för att ansluta till en databas i Azure SQL Database och använda Transact-SQL-uttryck för att fråga data.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>TLS-överväganden för SQL Database-anslutning

Transport Layer Security (TLS) används av alla driv rutiner som Microsoft tillhandahåller eller stöder för att ansluta till en databas i Azure SQL Database. Ingen särskild konfiguration krävs. För alla anslutningar till en SQL Server instans, en databas i Azure SQL Database eller en instans av Azure SQL-hanterad instans, rekommenderar vi att alla program ställer in följande konfigurationer eller deras motsvarigheter:

- **Kryptera = På**
- **TrustServerCertificate = Av**

Vissa system använder andra men likvärdiga nyckelord för dessa konfigurationsnyckelord. Dessa konfigurationer ser till att klientdrivrutinen bekräftar identiteten för TLS-certifikatet som togs emot från servern.

Vi rekommenderar också att du inaktiverar TLS 1.1 och 1.0 på klienten om du behöver följa datasäkerhetsstandarden PCI-DSS (Payment Card Industry – Data Security Standard).

Drivrutiner som inte kommer från Microsoft kanske inte använder TLS som standard. Detta är viktigt att ha i åtanke vid anslutning till Azure SQL Database. Du kanske inte kan ändra dessa anslutningsinställningar i program med inbäddade drivrutiner. Vi rekommenderar att du kontrollerar säkerheten i sådana drivrutiner och program innan du använder dem i system som hanterar känsliga data.

## <a name="libraries"></a>Bibliotek

Du kan använda olika bibliotek och ramverk för att ansluta till Azure SQL Database eller Azure SQL-hanterad instans. Kolla in våra [inledande självstudier](https://aka.ms/sqldev) om du snabbt vill komma igång med programmeringsspråk som C#, Java, Node.js, PHP och Python. Skapa sedan en app med hjälp av SQL Server i Linux eller Windows, eller Docker i macOS.

I följande tabell ser du vilka anslutningsbibliotek eller *drivrutiner* som klientprogram kan använda i olika språk för att ansluta till och använda SQL Server lokalt eller i molnet. Du kan använda dem i Linux, Windows och Docker när du vill ansluta till Azure SQL Database eller Azure SQL Data Warehouse.

| Språk | Plattform | Ytterligare resurser | Ladda ned | Kom igång |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET för SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Ladda ned](https://www.microsoft.com/net/download/) | [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Ladda ned](https://go.microsoft.com/fwlink/?linkid=852460) |  [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-drivrutin för SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Ladda ned](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Installera](https://msdn.microsoft.com/library/mt652094.aspx) |  [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-drivrutin](https://msdn.microsoft.com/library/mt652092.aspx) | Installationsval: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Installera](https://msdn.microsoft.com/library/mt711041.aspx) | [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Ladda ned](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

I följande tabell visas exempel på ramverk för objekt Relations mappning (ORM) och webb ramverk som klient program kan använda med SQL Server, Azure SQL Database, Azure SQL-hanterad instans eller Azure Synapse Analytics. Du kan använda ramverken på Linux, Windows eller Docker.

| Språk | Plattform | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Nästa steg

- Information om anslutningsarkitekturen finns i [Azure SQL Database Connectivity Architecture](connectivity-architecture.md) (Anslutningsarkitektur för Azure SQL Database).
- Hitta [SQL Server driv rutiner](https://msdn.microsoft.com/library/mt654049.aspx) som används för att ansluta från klient program.
- Anslut till Azure SQL Database:
  - [Anslut till SQL Database med .NET (C#)](connect-query-dotnet-core.md)
  - [Ansluta till SQL Database med PHP](connect-query-php.md)
  - [Ansluta till SQL Database med Node.js](connect-query-nodejs.md)
  - [Ansluta till SQL Database med Java](connect-query-java.md)
  - [Anslut till SQL Database med Python](connect-query-python.md)
  - [Ansluta till SQL Database med Ruby](connect-query-ruby.md)
- Kodexempel för omprövningslogik:
  - [Anslut elastiskt för att SQL Database med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Anslut elastiskt för att SQL Database med PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
