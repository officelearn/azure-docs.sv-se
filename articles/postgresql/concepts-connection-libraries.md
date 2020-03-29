---
title: Anslutningsbibliotek - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs flera bibliotek och drivrutiner som du kan använda när du kodar program för att ansluta och fråga Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768902"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Anslutningsbibliotek för Azure Database för PostgreSQL - Single Server
I den här artikeln visas bibliotek och drivrutiner som utvecklare kan använda för att utveckla program för att ansluta till och fråga Azure Database för PostgreSQL.

## <a name="client-interfaces"></a>Klientgränssnitt
De flesta språkklientbibliotek som används för att ansluta till PostgreSQL-servern är externa projekt och distribueras oberoende av dem. Biblioteken i listan stöds på Plattformarna Windows, Linux och Mac för anslutning till Azure Database för PostgreSQL. Flera snabbstartsexempel visas i avsnittet Nästa steg.

| **Språk** | **Klientgränssnitt** | **Ytterligare information** | **Ladda ned** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [Psycopg](http://initd.org/psycopg/) | DB API 2.0-kompatibel | [Ladda ned](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Databastillägg | [Installera](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm-paket](https://www.npmjs.com/package/pg) | Ren JavaScript-klient som inte blockerar | [Installera](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | JDBC-drivrutin typ 4 | [Hämta](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg pärla](https://deveiate.org/code/pg/) | Ruby-gränssnitt | [Ladda ned](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Paket pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres förare | [Installera](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql (1)](https://www.npgsql.org/) | ADO.NET-dataleverantör | [Ladda ned](https://www.microsoft.com/net/) |
| ODBC | [Psqlodbc](https://odbc.postgresql.org/) | ODBC-drivrutin | [Ladda ned](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [Libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primärt C-språkgränssnitt | Ingår |
| C++ | [Libpqxx](http://pqxx.org/) | Gränssnitt för ny stil i C++ | [Ladda ned](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Nästa steg
Läs de här snabbstarterna om hur du ansluter till och frågar Azure Database för PostgreSQL med hjälp av ditt språk:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
