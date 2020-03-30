---
title: Anslutningsbibliotek - Azure Database för MySQL
description: I den här artikeln visas varje bibliotek eller drivrutin som klientprogram kan använda när de ansluter till Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537201"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Anslutningsbibliotek för Azure Database för MySQL
I den här artikeln visas varje bibliotek eller drivrutin som klientprogram kan använda när de ansluter till Azure Database för MySQL.

## <a name="client-interfaces"></a>Klientgränssnitt
MySQL erbjuder standard databasdrivrutinsanslutning för att använda MySQL med program och verktyg som är kompatibla med branschstandarder ODBC och JDBC. Alla system som fungerar med ODBC eller JDBC kan använda MySQL.

| **Språk** | **Plattform** | **Ytterligare resurs** | **Ladda ned** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MySQL native drivrutin för PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Ladda ned](https://secure.php.net/downloads.php) |
| ODBC | Windows-, Linux-, Mac OS X- och Unix-plattformar | [Utvecklarhandboken för MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Utvecklarhandledning för MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Plattform oberoende | [Utvecklarhandledning för MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/nod-mysql2 sidorares/nod-mysql2 sidorares/nod-mysql2 sidorare](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Ladda ned](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Utvecklarhandboken för MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Utvecklarhandledning för MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Ladda ned](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Utvecklarhandledning för MySQL Connector/C](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Ladda ned](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows-, Linux-, Mac OS X- och Unix-plattformar | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Ladda ned](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Nästa steg
Läs de här snabbstarterna om hur du ansluter till och frågar Azure Database för MySQL med hjälp av ditt språk:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

