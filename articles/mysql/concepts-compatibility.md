---
title: Drivrutins- och verktygskompatibilitet - Azure Database för MySQL
description: I den här artikeln beskrivs MySQL-drivrutiner och hanteringsverktyg som är kompatibla med Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: e8917a0a5678c4c6b72352a0d4c1523bfea3c96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537218"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-drivrutiner och hanteringsverktyg som är kompatibla med Azure Database för MySQL
I den här artikeln beskrivs drivrutiner och hanteringsverktyg som är kompatibla med Azure Database for MySQL.

## <a name="mysql-drivers"></a>MySQL-drivrutiner
Azure Database for MySQL använder världens mest populära community-utgåva av MySQL-databasen. Därför är det kompatibelt med en mängd olika programmeringsspråk och drivrutiner. Målet är att stödja de tre senaste versionerna MySQL drivrutiner, och insatser med författare från öppen källkod samfundet att ständigt förbättra funktionaliteten och användbarheten av MySQL drivrutiner fortsätta. En lista över drivrutiner som har testats och befunnits vara kompatibla med Azure Database för MySQL 5.6 och 5.7 finns i följande tabell:

| **Programmeringsspråk** | **Drivrutin** | **Länkar** | **Kompatibla versioner** | **Inkompatibla versioner** | **Obs!** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7,x | 5.3 | För PHP 7.0-anslutning med SSL MySQLi, lägg till MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT i anslutningssträngen. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> SUB set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` alternativ till false.|
| .NET | Async MySQL-anslutning för .NET | https://github.com/mysql-net/MySqlConnector <br> [Installationspaket från Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 och efter | 0.26.5 och tidigare | |
| .NET | MySQL-kontakt/NET | https://github.com/mysql/mysql-connector-net | 6.6.3 ,7.0 ,8.0 |  | Ett kodningsfel kan orsaka att anslutningar misslyckas på vissa Windows-system som inte är UTF8. |
| Node.js | mysqljs (alla) | https://github.com/mysqljs/mysql/ <br> Installationspaket från NPM:<br> Kör `npm install mysql` från NPM | 2.15 | 2.14.1 och tidigare | |
| Node.js | nod-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4+ | | |
| Go | Gå MySQL-drivrutin | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 och tidigare | Används `allowNativePasswords=true` i anslutningssträngen för version 1.3. Version 1.4 innehåller en `allowNativePasswords=true` korrigering och behövs inte längre. |
| Python | MySQL-anslutning/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2, använd 8.0.16+ med MySQL 8.0  | 1.2.2 och tidigare | |
| Python | PyMysql (3000) | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3+ | 0.9.0 - 0.9.2 (regression i web2py) | |
| Java | MariaDB-kontakt/J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 och tidigare | | 
| Java | MySQL-kontakt/J | https://github.com/mysql/mysql-connector-j | 5.1.21+, använd 8.0.17+ med MySQL 8.0 | 5.1.20 och lägre | |
| C | MySQL-kontakt/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2+ | | |
| C | MySQL-kontakt/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29+ | | |
| C++ | MySQL-kontakt/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9+ | 1.1.3 och lägre | | 
| C++ | MySQL++| https://tangentsoft.net/mysql++ | 3.2.3+ | | |
| Ruby | Mysql2 | https://github.com/brianmario/mysql2 | 0.4.10+ | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16+ | | |
| Swift | mysql-snabb | https://github.com/novi/mysql-swift | 0.7.2+ | | |
| Swift | ånga / mysql | https://github.com/vapor/mysql-kit | 2.0.1+ | | |

## <a name="management-tools"></a>Hanteringsverktyg
Kompatibilitetsfördelen omfattar även verktyg för databashantering. Dina befintliga verktyg bör fortsätta att fungera med Azure Database for MySQL, så länge databasmanipuleringen fungerar inom gränserna för användarbehörigheter. Tre vanliga databashanteringsverktyg som har testats och visat sig vara kompatibla med Azure Database för MySQL 5.6 och 5.7 visas i följande tabell:

|                                     | **MySQL Workbench 6.x och uppåt** | **Navicat 12 (På andra)** | **PHPMyAdmin 4.x och uppåt** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Skapa, uppdatera, läsa, skriva, ta bort | X | X | X |
| SSL-anslutning | X | X | X |
| Automatisk komplettering av SQL Query | X | X |  |
| Importera och exportera data | X | X | X | 
| Exportera till flera format | X | X | X |
| Säkerhetskopiera och återställ |  | X |  |
| Parametrar för visningsserver | X | X | X |
| Visa klientanslutningar | X | X | X |

## <a name="next-steps"></a>Nästa steg

- [Felsöka anslutningsproblem till Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)