---
title: MySQL-drivrutiner och verktyg för kompatibilitetshantering
description: Den här artikeln beskriver MySQL drivrutiner och hanteringsverktyg som är kompatibla med Azure-databas för MySQL.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: a133ccdd0b40f9a878ba627e6c093787316aea35
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264128"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-drivrutiner och hanteringsverktyg som är kompatibla med Azure-databas för MySQL
Den här artikeln beskriver drivrutiner och hanteringsverktyg som är kompatibla med Azure-databas för MySQL.

## <a name="mysql-drivers"></a>MySQL-drivrutiner
Azure-databas för MySQL använder världens populäraste community-versionen av MySQL-databas. Därför är kompatibla med en mängd olika programmeringsspråk och drivrutiner. Målet är att stödja de tre senaste versionerna MySQL drivrutiner och fortsätta arbetet med öppen källkod författare att ständigt förbättra funktionalitet och användbarhet MySQL-drivrutiner. En lista över drivrutiner som har testats och befunnits vara kompatibla med Azure-databas för MySQL 5.6 och 5.7 finns i följande tabell:

| **Drivrutinen** | **Länkar** | **Kompatibla versioner** | **En versioner** | **Anteckningar** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://php.net/downloads.php | 5.5 5.6 7.x | 5.3 | Lägg till MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT i anslutningssträngen för PHP 7.0-anslutning med SSL MySQLi. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` alternativet till false.|
| .Net | [MySqlConnector på GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Installationspaketet från Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 och efter | 0.26.5 och före | |
| Nodejs |  [MySQLjs på GitHub](https://github.com/mysqljs/mysql/releases) <br> Installationspaketet från NPM:<br> Kör `npm install mysql` från NPM | 2.15 | 2.14.1 och före | |
| GÅ | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 och före | Använd allowNativePasswords = true i anslutningssträngen |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 och före | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 och före | |

## <a name="management-tools"></a>Hanteringsverktyg
Kompatibilitet fördelen utvidgar samt databas-hanteringsverktyg. Befintliga verktyg bör fortsätta att arbeta med Azure-databas för MySQL, förutsatt att hantera databasen fungerar inom gränserna för användarbehörigheter. Tre vanliga databas-hanteringsverktyg som har testats och befunnits vara kompatibla med Azure-databas för MySQL 5.6 och 5.7 visas i följande tabell:

|                                     | **MySQL-arbetsstationen 6.x och uppåt** | **Navicat 12** | **PHPMyAdmin 4.x och uppåt** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Skapa, uppdatera, läsa, skriva, ta bort | X | X | X |
| SSL-anslutning | X | X | X |
| Autokomplettering för SQL-fråga | X | X |  |
| Importera och exportera Data | X | X | X |
| Exportera till flera olika format | X | X | X |
| Säkerhetskopiera och återställ |  | X |  |
| Visa parametrar för Server | X | X | X |
| Visa klientanslutningar | X | X | X |
