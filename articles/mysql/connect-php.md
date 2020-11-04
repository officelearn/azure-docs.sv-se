---
title: 'Snabb start: Anslut med PHP-Azure Database for MySQL'
description: I den här snabbstarten finns ett kodexempel i PHP som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: ec406208f862eac2450cc6352f13f3596a7c9775
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337395"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Snabb start: använda PHP för att ansluta och fråga efter data i Azure Database for MySQL
Den här snabbstarten visar hur du ansluter till en Azure Database för MySQL med hjälp av ett [PHP](https://secure.php.net/manual/intro-whatis.php)-program. Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Det här avsnittet förutsätter att du är van att utveckla i PHP och att du saknar erfarenhet av Azure Database for MySQL.

## <a name="prerequisites"></a>Krav
I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa en Azure Database för MySQL med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Se till att den IP-adress som du ansluter från har lagts till i serverns brand Väggs regler med hjälp av [Azure Portal](./howto-manage-firewall-using-portal.md) eller [Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="install-php"></a>Installera PHP
Installera PHP på din server, eller skapa en Azure-[webbapp](../app-service/overview.md) som innehåller PHP.

### <a name="macos"></a>macOS
- Hämta [PHP 7.1.4](https://secure.php.net/downloads.php).
- Installera PHP och se php- [handboken](https://secure.php.net/manual/install.macosx.php) för ytterligare konfiguration.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Hämta [php-7.1.4 (x64) version som inte är tråd säker (x64)](https://secure.php.net/downloads.php).
- Installera PHP och se php- [handboken](https://secure.php.net/manual/install.unix.php) för ytterligare konfiguration.

### <a name="windows"></a>Windows
- Hämta [php-7.1.4 (x64) version som inte är tråd säker (x64)](https://windows.php.net/download#php-7.1).
- Installera PHP och se php- [handboken](https://secure.php.net/manual/install.windows.php) för ytterligare konfiguration.

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Skaffa den information som du behöver för att ansluta till Azure Database för MySQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser**. Sök sedan efter den server som du skapade (till exempel **mydemoserver** ).
3. Klicka på servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database för MySQL-servernamn":::

## <a name="connect-and-create-a-table"></a>Ansluta och skapa en tabell
Använd följande kod för att ansluta och skapa en tabell med hjälp av SQL-instruktionen **CREATE TABLE**. 

Koden använder klassen **MySQL Improved extension** (mysqli) som ingår i PHP. Metoderna [mysqli_init](https://secure.php.net/manual/mysqli.init.php) och [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) anropas för att ansluta till MySQL. Sedan anropas metoden [mysqli_query](https://secure.php.net/manual/mysqli.query.php) för att köra frågan. Sedan anropas metoden [mysqli_close](https://secure.php.net/manual/mysqli.close.php) för att stänga anslutningen.

Ersätt parametrarna host, username, password och db_name med dina egna värden. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Infoga data
Använd följande kod för att ansluta och infoga data med en **INSERT** -SQL-instruktion.

Koden använder klassen **MySQL Improved extension** (mysqli) som ingår i PHP. I koden används metoden [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) för att skapa en förberedd insert-instruktion och sedan binds parametrarna för varje infogat kolumnvärde med metoden [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Sedan körs instruktionen med metoden [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) och efteråt stängs instruktionen med metoden [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Ersätt parametrarna host, username, password och db_name med dina egna värden. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Läsa data
Använd följande kod för att ansluta och läsa data med en **SELECT** -SQL-instruktion.  Koden använder klassen **MySQL Improved extension** (mysqli) som ingår i PHP. Metoden [mysqli_query](https://secure.php.net/manual/mysqli.query.php) används för att utföra sql-frågan och metoden [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) används för att hämta de resulterande raderna.

Ersätt parametrarna host, username, password och db_name med dina egna värden. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att ansluta och uppdatera data med en **UPDATE** -SQL-instruktion.

Koden använder klassen **MySQL Improved extension** (mysqli) som ingår i PHP. Metoden [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) används för att skapa en förberedd update-instruktion och sedan binds parametrarna för varje uppdaterat kolumnvärde med metoden [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Sedan körs instruktionen med metoden [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) och efteråt stängs instruktionen med metoden [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Ersätt parametrarna host, username, password och db_name med dina egna värden. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och läsa data med en **DELETE** -SQL-instruktion. 

Koden använder klassen **MySQL Improved extension** (mysqli) som ingår i PHP. Metoden [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) används för att skapa en förberedd delete-instruktion och sedan binds parametrarna för where-satsen med metoden [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Sedan körs instruktionen med metoden [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) och efteråt stängs instruktionen med metoden [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Ersätt parametrarna host, username, password och db_name med dina egna värden. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa alla resurser som används under den här snabb starten tar du bort resurs gruppen med hjälp av följande kommando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Ansluta till Azure Database for MySQL via SSL](howto-configure-ssl.md)
