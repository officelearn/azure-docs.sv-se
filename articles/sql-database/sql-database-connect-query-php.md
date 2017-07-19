---
title: "Ansluta till Azure SQL Database med hjälp av PHP | Microsoft Docs"
description: "Anger ett PHP-kodexempel som du kan använda för att ansluta till och fråga en Azure SQL-databas."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 5b90c9b49448c6bc225f9d6ba7227782b81fc5ed
ms.contentlocale: sv-se
ms.lasthandoff: 06/15/2017


---
# <a name="azure-sql-database-use-php-to-connect-and-query-data"></a>Azure SQL Database: Använd PHP för att ansluta och skicka frågor till data

Den här snabbstarten visar hur du använder [PHP](http://php.net/manual/en/intro-whatis.php) för att ansluta till en Azure SQL-databas. Använd sedan Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Microsoft-, Mac OS- och Ubuntu Linux-plattformar.

## <a name="prerequisites"></a>Krav

Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter som utgångspunkt:

- [Skapa DB – Portal](sql-database-get-started-portal.md)
- [Skapa DB – CLI](sql-database-get-started-cli.md)
- [Skapa DB – PowerShell](sql-database-get-started-powershell.md)

## <a name="install-php-and-database-communications-software"></a>Installera PHP och programvara för kommunikation med databasen

Stegen i det här avsnittet förutsätter att du är bekant med att utveckla med PHP och att du är nybörjare när det gäller att arbeta med Azure SQL Database. Om du är nybörjare på utveckla med PHP går du till [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Skapa en app med SQL Server) och väljer **PHP** och sedan ditt operativsystem.

### <a name="mac-os"></a>**Mac OS**
Öppna terminalen och ange följande kommandon för att installera **brew**, **Microsoft ODBC-drivrutin för Mac** och **Microsoft PHP-drivrutiner för SQL Server**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Ange följande kommandon för att installera **Microsoft ODBC-drivrutin** för Linux och **Microsoft PHP-drivrutiner** för SQL Server.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- Installera PHP 7.1.1 (x 64) [från WebPlatform Installer](https://www.microsoft.com/web/downloads/platform.aspx?lang=) 
- Installera [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339). 
- Hämta säkra icke-tråd-dll-filer för [Microsoft PHP-drivrutin för SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) och placera de binära filerna i mappen PHP\v7.x\ext.
- Redigera sedan din php.ini-fil (C:\Program Files\PHP\v7.1\php.ini) genom att lägga till referensen i dll-filen. Exempel:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

Du bör nu ha alla dll: er som registrerats med PHP.

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet, databasnamnet och inloggningsinformationen i nästa procedurer.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. Granska serverns fullständiga namn på sidan **Översikt** för databasen, se bilden nedan. Om du hovrar över servernamnet visas alternativet **Kopiera genom att klicka**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Om du glömmer inloggningsinformationen för din server öppnar du serversidan i SQL Database. Där ser du administratörsnamnet för servern och kan återställa lösenordet vid behov.     
    
## <a name="select-data"></a>Välj data
Använd följande kod för att söka efter de 20 främsta produkterna med funktionen [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) och transact-SQL-instruktionen [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Funktionen sqlsrv_query används för att hämta en resultatmängd från en fråga mot SQL-databasen. Den här funktionen accepterar en fråga och returnerar en resultatuppsättning som kan upprepas med hjälp av [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php). Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT. 

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


## <a name="insert-data"></a>Infoga data
Använd följande kod för att infoga en ny produkt i tabellen SalesLT.Product med funktionen [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) och transact-SQL-instruktionen [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT. 

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
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att uppdatera data i Azure SQL-databasen med funktionen [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) och transact-SQL-instruktionen [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT.

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
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="delete-data"></a>Ta bort data
Med följande kod raderar du den nya produkt du tidigare lade till med funktionen [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) och Transact-SQL-instruktionen [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) . Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT.

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
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

## <a name="next-steps"></a>Nästa steg
- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft PHP-drivrutiner för SQL Server](https://github.com/Microsoft/msphpsql/)
- [Rapportera problem/ställ frågor](https://github.com/Microsoft/msphpsql/issues)


