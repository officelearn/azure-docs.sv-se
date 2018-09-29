---
title: Fråga Azure SQL Database med PHP | Microsoft Docs
description: Det här avsnittet visar hur du använder PHP för att skapa ett program som ansluter till en Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 2fc7b961df003fe05bc4ad4e49b9debb74c952fd
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063242"
---
# <a name="use-php-to-query-an-azure-sql-database"></a>Använd PHP för att fråga en Azure SQL-databas

Den här snabbstarten visar hur du använder [PHP](http://php.net/manual/en/intro-whatis.php) för att skapa ett program som ansluter till en Azure SQL-databas och använder Transact-SQL-instruktioner för att köra frågor mot data.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan genomföra den här snabbstarten:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- En [brandväggsregel på servernivå](sql-database-get-started-portal-firewall.md) för den offentliga IP-adressen till datorn som du använder för den här snabbstarten.

- Du har installerat PHP och relaterad programvara för ditt operativsystem:

    - **MacOS**: Först installerar du Homebrew och PHP, sedan ODBC-drivrutinen och SQLCMD och sedan installerar du PHP-drivrutinen för SQL Server. Se [steg 1.2, 1.3 och 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/mac/).
    - **Ubuntu**: Först installerar du PHP och andra paket som krävs, sedan installerar du PHP-drivrutinen för SQL Server. Se [steg 1.2 och 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).
    - **Windows**: installera den senaste versionen av PHP för IIS Express, den senaste versionen av Microsoft-drivrutiner för SQL Server i IIS Express, Chocolatey, ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).    

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Infoga kod för att fråga SQL-databas

1. Skapa en ny fil, **sqltest.php**, i valfri textredigerare.  

2. Ersätt innehållet med följande kod och lägg till lämpliga värden för din server, databas, användare och lösenord.

   ```PHP
   <?php
   $serverName = "your_server.database.windows.net";
   $connectionOptions = array(
       "Database" => "your_database",
       "Uid" => "your_username",
       "PWD" => "your_password"
   );
   //Establishes the connection
   $conn = sqlsrv_connect($serverName, $connectionOptions);
   $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid";
   $getResults= sqlsrv_query($conn, $tsql);
   echo ("Reading data from table" . PHP_EOL);
   if ($getResults == FALSE)
       echo (sqlsrv_errors());
   while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
   }
   sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Kör koden

1. Kör följande kommandon i kommandotolken:

   ```php
   php sqltest.php
   ```

2. Kontrollera att de 20 översta raderna returneras och stäng sedan programfönstret.

## <a name="next-steps"></a>Nästa steg
- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft PHP-drivrutiner för SQL Server](https://github.com/Microsoft/msphpsql/)
- [Rapportera problem eller ställ frågor](https://github.com/Microsoft/msphpsql/issues)
- [Exempel på logik för omprövning: Ansluta elastiskt till SQL med PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

