---
title: Anslutningsbibliotek för SQL-databas | Microsoft Docs
description: Länkar för hämtningar av moduler som gör anslutningen till SQL Server och SQL-databas från ett stort antal klienter som programmeringsspråk.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 29a6c7107a57babb5dc81fa22146897d3958f0f0
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036349"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Anslutningsbibliotek och ramverk för SQL Server

Kolla in våra [få igång Självstudier](https://aka.ms/sqldev) att snabbt komma igång med programmeringsspråk, till exempel C#, Java, Node.js, PHP och Python. Skapa sedan en app med hjälp av SQL Server på Linux eller Windows eller Docker på macOS.

I följande tabell visas anslutningsbibliotek eller *drivrutiner* att klientprogram kan använda från en mängd olika språk för att ansluta till och använda SQL Server som körs lokalt eller i molnet. Du kan använda dem på Linux, Windows eller Docker och använda dem för att ansluta till Azure SQL Database och Azure SQL Data Warehouse. 

| Språk | Plattform | Ytterligare resurser | Ladda ned | Kom igång |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET för SQLServer](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Ladda ned](https://www.microsoft.com/net/download/) | [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Ladda ned](https://go.microsoft.com/fwlink/?linkid=852460) |  [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP-SQL-drivrutin för SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Ladda ned](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Installera](https://msdn.microsoft.com/library/mt652094.aspx) |  [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [SQL Python-drivrutinen](https://msdn.microsoft.com/library/mt652092.aspx) | Installera val: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Installera](https://msdn.microsoft.com/library/mt711041.aspx) | [Kom igång](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-drivrutin för SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Ladda ned](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

I följande tabell visas exempel objektrelationell mappning (ORM) ramverk och webbramverk som klientprogram kan använda med SQL Server som körs lokalt eller i molnet. Du kan använda ramverk på Linux, Windows eller Docker och använda dem för att ansluta till SQL Database och SQL Data Warehouse. 

| Språk | Plattform | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Viloläge ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Relaterade länkar
- [SQL Server-drivrutiner](https://msdn.microsoft.com/library/mt654049.aspx) som används för att ansluta från klientprogram
- Anslut till SQL Database:
    - [Ansluta till SQL Database med .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Ansluta till SQL Database med PHP](sql-database-connect-query-php.md)
    - [Ansluta till SQL Database med Node.js](sql-database-connect-query-nodejs.md)
    - [Ansluta till SQL Database med Java](sql-database-connect-query-java.md)
    - [Anslut till SQL Database med Python](sql-database-connect-query-python.md)
    - [Ansluta till SQL Database med Ruby](sql-database-connect-query-ruby.md)
- Gör logic kodexempel:
    - [Ansluta Elastiskt till SQL med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Ansluta Elastiskt till SQL med PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

