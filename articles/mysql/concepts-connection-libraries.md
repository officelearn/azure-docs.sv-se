---
title: Anslutningsbibliotek för Azure Database for MySQL
description: Den här artikeln visas alla bibliotek eller en drivrutin som klientprogram kan använda när du ansluter till Azure Database för MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 14515aefe9635160cf99a630b0742d23352532cf
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985972"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Anslutningsbibliotek för Azure Database for MySQL
Den här artikeln visas alla bibliotek eller en drivrutin som klientprogram kan använda när du ansluter till Azure Database för MySQL.

## <a name="client-interfaces"></a>Klientgränssnitt
MySQL erbjuder standarddatabasen drivrutinen anslutning för att använda MySQL med program och verktyg som är kompatibla med branschstandarder ODBC och JDBC. Alla system som fungerar med ODBC eller JDBC kan använda MySQL.

| **Språk** | **Plattform** | **Ytterligare resurser** | **Ladda ned** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Interna MySQL-drivrutin för PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Ladda ned](https://secure.php.net/downloads.php) |
| ODBC | Plattformar som Windows, Linux, Mac OS X och Unix | [Utvecklarguide för MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Utvecklarguide för MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Plattformsoberoende | [Utvecklarguide för MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Ladda ned](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Utvecklarguide för MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Utvecklarguide för MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Utvecklarguide för MySQL Connector/C](https://dev.mysql.com/doc/connector-c/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/c/)
| Perl | Plattformar som Windows, Linux, Mac OS X och Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Ladda ned](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Nästa steg
Läs de här snabbstarterna på hur du ansluter till och fråga efter Azure Database för MySQL med hjälp av valfritt språk:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [gå](./connect-go.md)

