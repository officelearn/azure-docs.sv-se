---
title: Anslutningsbibliotek för Azure Database for PostgreSQL
description: Den här artikeln beskriver flera bibliotek och drivrutiner som utvecklare kan använda när kodning program att ansluta och fråga efter Azure Database för PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: edbd3b36e1c40cda3b39c85f3deb4c9e8540fd1b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984969"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Anslutningsbibliotek för Azure Database for PostgreSQL
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

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [gå](./connect-go.md)
