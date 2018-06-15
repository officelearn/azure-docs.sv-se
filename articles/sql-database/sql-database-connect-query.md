---
title: Snabbstarter för Azure SQL Database-anslutning och fråga | Microsoft Docs
description: Snabbstarterna för Azure SQL Database visar hur du ansluter till och frågar en Azure SQL-databas.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc
ms.devlang: ''
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: carlrab
ms.openlocfilehash: ec39c5ad0771c2bc78655e52c58949db6e9b3353
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186019"
---
# <a name="azure-sql-database-connect-and-query-quickstarts"></a>Snabbstarter för Azure SQL Database-anslutning och fråga

Följande dokument innehåller länkar till Azure-exempel som beskriver hur du ansluter till och frågar en Azure SQL-databas. Det innehåller också några rekommendationer för TLS (Transport Level Security).

## <a name="quickstarts"></a>Snabbstarter

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Den här snabbstarten visar hur du använder SSMS för att ansluta till en Azure SQL-databas och sedan använder Transact-SQL-uttryck för att skicka frågor mot, infoga, uppdatera och ta bort data i databasen.|
|[SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Den här snabbstarten visar hur du använder SQL Operations Studio (förhandsversion) för att ansluta till en Azure SQL-databas och sedan använder Transact-SQL-instruktioner (T-SQL) till att skapa den TutorialDB som används i självstudierna för SQL Operations Studio (förhandsversion).|
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

## <a name="next-steps"></a>Nästa steg

Information om anslutningsarkitekturen finns i [Azure SQL Database Connectivity Architecture](sql-database-connectivity-architecture.md) (Anslutningsarkitektur för Azure SQL Database).