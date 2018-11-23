---
title: MySQL-drivrutiner och -verktyg för kompatibilitetshantering
description: Den här artikeln beskriver de MySQL drivrutiner och hanteringsverktyg som är kompatibla med Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 11/21/2018
ms.openlocfilehash: 4232e1850a1354fe247258d46b280e1bfa653972
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290832"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-drivrutiner och hanteringsverktyg som är kompatibla med Azure Database for MySQL
Den här artikeln beskriver de drivrutiner och hanteringsverktyg som är kompatibla med Azure Database for MySQL.

## <a name="mysql-drivers"></a>MySQL-drivrutiner
Azure Database för MySQL använder världens mest populära community-utgåvan av MySQL-databas. Därför är kompatibla med en mängd olika programmeringsspråk och drivrutiner. Målet är att stödja de tre senaste versionerna MySQL-drivrutiner och fortsätta ansträngningar med författare från communityn för öppen källkod för att ständigt förbättra funktioner och användbarhet för MySQL-drivrutiner. En lista över drivrutiner som har testats och visat sig vara kompatibla med Azure Database for MySQL 5.6 och 5.7 finns i följande tabell:

| **Drivrutinen** | **Länkar** | **Kompatibla versioner** | **Inkompatibla versioner** | **Anteckningar** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | PHP 7.0 anslutningen med SSL MySQLi, lägger du till MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT i anslutningssträngen. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` alternativet på FALSKT.|
| .Net | [MySqlConnector på GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Installationspaketet från Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 och efter | 0.26.5 och före | |
| MySQL Connector/NET | [MySQL Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | En kodning bugg kan medföra att anslutningar misslyckas på vissa icke - UTF8-Windows-System. |
| Nodejs |  [MySQLjs på GitHub](https://github.com/mysqljs/mysql/) <br> Installationspaketet från NPM:<br> Kör `npm install mysql` från NPM | 2.15 | 2.14.1 och före | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 och före | Använd allowNativePasswords = true i anslutningssträngen |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 och före | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 och före | |

## <a name="management-tools"></a>Hanteringsverktyg
Fördelen med kompatibilitet utökar till verktygen för databashantering samt. Dina befintliga verktyg bör fortsätta att fungera med Azure Database för MySQL, så länge databasmanipulering av fungerar inom användarbehörigheter. Tre vanliga verktygen för databashantering som har testats och visat sig vara kompatibla med Azure Database for MySQL 5.6 och 5.7 visas i följande tabell:

|                                     | **MySQL Workbench 6.x eller senare** | **Navicat 12** | **PHPMyAdmin 4.x och upp** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Skapa, uppdatera, läsa, skriva, ta bort | X | X | X |
| SSL-anslutning | X | X | X |
| SQL-fråga automatiskt slutförande | X | X |  |
| Importera och exportera Data | X | X | X |
| Exportera till flera format | X | X | X |
| Säkerhetskopiera och återställ |  | X |  |
| Visa serverparametrar | X | X | X |
| Visa klientanslutningar | X | X | X |
