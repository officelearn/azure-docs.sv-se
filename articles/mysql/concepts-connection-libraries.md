---
title: Anslutningsbibliotek för Azure-databas för MySQL
description: Den här artikeln visas alla bibliotek eller en drivrutin som klientprogram som kan användas vid anslutning till Azure-databas för MySQL.
services: mysql
author: mswutao
ms.author: wutao
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: c826bf1cf17230563b608e764c443b6166f13924
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264011"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Anslutningsbibliotek för Azure-databas för MySQL
Den här artikeln visas alla bibliotek eller en drivrutin som klientprogram som kan användas vid anslutning till Azure-databas för MySQL.

## <a name="client-interfaces"></a>Klient-gränssnitt
MySQL erbjuder standard drivrutinen databasanslutning för att använda MySQL med program och verktyg som är kompatibla med branschstandarder ODBC och JDBC. Alla system som fungerar med ODBC eller JDBC kan använda MySQL.

| **Språk** | **Plattform** | **Ytterligare resurser** | **Ladda ned** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Intern MySQL-drivrutin för PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Ladda ned](http://php.net/downloads.php) |
| ODBC | Windows, Linux, Mac OS X och Unix-plattformar | [MySQL-koppling/ODBC-Guide för utvecklare](https://dev.mysql.com/doc/connector-odbc/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Utvecklarhandbok för MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Plattformsoberoende | [Utvecklarhandbok för MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Ladda ned](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Utvecklarhandbok för MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [MySQL-koppling/C++ Guide för utvecklare](https://dev.mysql.com/doc/connector-cpp/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Utvecklarhandbok för MySQL Connector/C](https://dev.mysql.com/doc/connector-c/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows, Linux, Mac OS X och Unix-plattformar | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Ladda ned](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Nästa steg
Läs dessa Snabbstart att ansluta till och fråga Azure-databas för MySQL med hjälp av ditt språk:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [gå](./connect-go.md)

