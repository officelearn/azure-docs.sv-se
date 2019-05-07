---
title: Anslutningsbibliotek för Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskriver flera bibliotek och drivrutiner som utvecklare kan använda när kodning program att ansluta och fråga Azure Database för PostgreSQL – enskild Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 85110126f9bdec225b1644860814cd89832132a1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073597"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Anslutningsbibliotek för Azure Database för PostgreSQL – enskild Server
Den här artikeln visar en lista över de bibliotek och drivrutiner som utvecklare kan använda för att utveckla program för att ansluta till och fråga efter Azure Database för PostgreSQL.

## <a name="client-interfaces"></a>Klientgränssnitt
De flesta språk klientbibliotek som används för att ansluta till PostgreSQL-servern är externa projekt och distribueras oberoende av varandra. Bibliotek i listan stöds på Windows, Linux och Mac-plattformar, för att ansluta till Azure Database för PostgreSQL. Flera quickstart-exempel finns i avsnittet nästa steg.

| **Språk** | **Klientgränssnitt** | **Ytterligare information** | **Ladda ned** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0-kompatibel | [Ladda ned](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Databas-tillägg | [Installera](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [PG npm-paket](https://www.npmjs.com/package/pg) | Ren JavaScript icke-blockerande klient | [Installera](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Typ 4 JDBC-drivrutinen | [Ladda ned](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG gem](https://deveiate.org/code/pg/) | Ruby-gränssnitt | [Ladda ned](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Paketet pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres-drivrutinen | [Installera](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | ADO.NET Data Provider | [Ladda ned](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC-drivrutin | [Ladda ned](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primärt gränssnitt för C-språk | Ingår |
| C++ | [libpqxx](http://pqxx.org/) | Ny formatmall C++-gränssnitt | [Ladda ned](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Nästa steg
Läs de här snabbstarterna på hur du ansluter till och fråga efter Azure Database för PostgreSQL med hjälp av valfritt språk:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
