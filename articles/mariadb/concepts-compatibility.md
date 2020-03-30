---
title: Kompatibilitet för drivrutiner och verktyg - Azure Database för MariaDB
description: I den här artikeln beskrivs MariaDB-drivrutinerna och hanteringsverktygen som är kompatibla med Azure Database för MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a0cee198f028fd90e04dac15e98d7cd33aee9201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532356"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB-drivrutiner och hanteringsverktyg som är kompatibla med Azure Database för MariaDB

I den här artikeln beskrivs drivrutiner och hanteringsverktyg som är kompatibla med Azure Database för MariaDB.

## <a name="mariadb-drivers"></a>MariaDB Drivrutiner

Azure Database för MariaDB använder community-utgåvan av MariaDB-servern. Därför är det kompatibelt med en mängd olika programmeringsspråk och drivrutiner. MariaDB:s API och protokoll är kompatibla med dem som används av MySQL. Det innebär att kontakter som fungerar med MySQL även ska fungera med MariaDB.

Målet är att stödja de tre senaste versionerna MariaDB drivrutiner, och insatser med författare från öppen källkod samfundet att ständigt förbättra funktionaliteten och användbarheten av MariaDB förare fortsätta. En lista över drivrutiner som har testats och befunnits vara kompatibla med Azure Database för MariaDB 10.2 finns i följande tabell:

**Drivrutin** | **Länkar** | **Kompatibla versioner** | **Inkompatibla versioner** | **Obs!**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5,5, 5,6, 7,x | 5.3 | För PHP 7.0-anslutning med SSL MySQLi, lägg till MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT i anslutningssträngen. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> SUB set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` alternativ till false.
.NET | [MySqlConnector på GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Installationspaket från Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 och efter | 0.26.5 och tidigare |
MySQL-kontakt/NET | [MySQL-kontakt/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Ett kodningsfel kan orsaka att anslutningar misslyckas på vissa Windows-system som inte är UTF8.
Node.js |  [MySQLjs på GitHub](https://github.com/mysqljs/mysql/) <br> Installationspaket från NPM:<br> Kör `npm install mysql` från NPM | 2.15 | 2.14.1 och tidigare
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 och tidigare | Används `allowNativePasswords=true` i anslutningssträngen för version 1.3. Version 1.4 innehåller en `allowNativePasswords=true` korrigering och behövs inte längre.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 och tidigare |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 och tidigare |

## <a name="management-tools"></a>Hanteringsverktyg

Kompatibilitetsfördelen omfattar även verktyg för databashantering. Dina befintliga verktyg bör fortsätta att fungera med Azure Database för MariaDB, så länge databasmanipuleringen fungerar inom gränserna för användarbehörigheter. I följande tabell visas tre vanliga verktyg för databashantering som har testats och befunnits vara kompatibla med Azure Database för MariaDB 10.2:

| | **MySQL Workbench 6.x och uppåt** | **Navicat 12 (På andra)** | **PHPMyAdmin 4.x och uppåt**
---|---|---|---
Skapa, uppdatera, läsa, skriva, ta bort | X | X | X
SSL-anslutning | X | X | X
Automatisk komplettering av SQL Query | X | X |
Importera och exportera data | X | X | X
Exportera till flera format | X | X | X
Säkerhetskopiera och återställ |  | X |
Parametrar för visningsserver | X | X | X
Visa klientanslutningar | X | X | X

## <a name="next-steps"></a>Nästa steg

- [Felsöka anslutningsproblem till Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)