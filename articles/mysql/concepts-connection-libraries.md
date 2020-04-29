---
title: Anslutnings bibliotek – Azure Database for MySQL
description: I den här artikeln visas varje bibliotek eller driv rutin som klient program kan använda vid anslutning till Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537201"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Anslutnings bibliotek för Azure Database for MySQL
I den här artikeln visas varje bibliotek eller driv rutin som klient program kan använda vid anslutning till Azure Database for MySQL.

## <a name="client-interfaces"></a>Klient gränssnitt
MySQL erbjuder standard anslutning för databas driv rutiner för att använda MySQL med program och verktyg som är kompatibla med bransch standarder ODBC och JDBC. Alla system som fungerar med ODBC eller JDBC kan använda MySQL.

| **Språk** | **Plattform** | **Ytterligare resurs** | **Ladda ned** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MySQL-inbyggd driv rutin för PHP – mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Ladda ned](https://secure.php.net/downloads.php) |
| ODBC | Windows-, Linux-, Mac OS X-och UNIX-plattformar | [Guide för MySQL Connector/ODBC-utvecklare](https://dev.mysql.com/doc/connector-odbc/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL Connector/net Developer Guide](https://dev.mysql.com/doc/connector-net/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Plattform oberoende | [MySQL Connector/J 5,1-guide för utvecklare](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/Node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Ladda ned](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [MySQL Connector/python-guide för utvecklare](https://dev.mysql.com/doc/connector-python/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guide för MySQL Connector/C++-utvecklare](https://dev.mysql.com/doc/connector-cpp/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guide för MySQL Connector/C-utvecklare](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Ladda ned](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows-, Linux-, Mac OS X-och UNIX-plattformar | [DBD:: MySQL](https://metacpan.org/pod/DBD::mysql) | [Ladda ned](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Nästa steg
Läs de här snabb starterna om hur du ansluter till och frågar Azure Database for MySQL med hjälp av ditt val av språk:

[PHP](./connect-php.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [Python](./connect-python.md) |  | [Go](./connect-go.md) [.NET (C#)](./connect-csharp.md) | [C++](connect-cpp.md)[Node.JS](./connect-nodejs.md).net (C#) python Node. js ruby C++ go |  

