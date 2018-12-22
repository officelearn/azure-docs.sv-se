---
title: Fråga Azure SQL-databas med PHP | Microsoft Docs
description: Så använder du PHP för att skapa ett program som ansluter till en Azure SQL-databas och köra frågor mot den med hjälp av SQL-instruktioner.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/28/2018
ms.openlocfilehash: b768b50af7ad6736e5cc3c885e6ac5016976f48a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958550"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Snabbstart: Använd PHP för att fråga en Azure SQL-databas

Den här artikeln visar hur du använder [PHP](http://php.net/manual/en/intro-whatis.php) för att ansluta till en Azure SQL-databas. Du kan sedan använda T-SQL-instruktioner för att köra frågor mot data.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra det här exemplet behöver du följande:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- En [brandväggsregel på servernivå](sql-database-get-started-portal-firewall.md) för den offentliga IP-adressen till den dator som du använder

- PHP-relaterad programvara installerad för ditt operativsystem:

    - **MacOS**, installera PHP, ODBC-drivrutinen och därefter PHP-drivrutinen för SQL Server. Se [Steg 1, 2 och 3](/sql/connect/php/installation-tutorial-linux-mac).

    - **Linux**, installera PHP, ODBC-drivrutinen och därefter PHP-drivrutinen för SQL Server. Se [Steg 1, 2 och 3](/sql/connect/php/installation-tutorial-linux-mac).

    - **Windows**, installera PHP för IIS Express och Chocolatey och installera därefter ODBC-drivrutinen och SQLCMD. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-database-connection"></a>Hämta databasanslutning

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="add-code-to-query-database"></a>Lägga till kod i frågedatabas

1. Skapa en ny fil, *sqltest.php*, i valfri textredigerare.  

1. Ersätt dess innehåll med följande kod. Lägg sedan till lämpliga värden för servern, databas, användare och lösenord.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
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

1. Kör appen i kommandotolken.

   ```bash
   php sqltest.php
   ```

1. Kontrollera att de 20 översta raderna returneras och stäng appfönstret.

## <a name="next-steps"></a>Nästa steg

- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)

- [Microsoft PHP-drivrutiner för SQL Server](https://github.com/Microsoft/msphpsql/)

- [Rapportera problem eller ställ frågor](https://github.com/Microsoft/msphpsql/issues)

- [Exempel på omprövningslogik: Ansluta elastiskt till SQL med PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
