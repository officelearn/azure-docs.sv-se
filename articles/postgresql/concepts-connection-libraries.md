---
title: Anslutningsbibliotek för Azure-databas för PostgreSQL
description: Den här artikeln beskriver flera bibliotek och drivrutiner som utvecklare kan använda när kodning Anslut och fråga Azure-databas för PostgreSQL-program.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7f0dd217a124f530ba009c9a5588691f604827e5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
ms.locfileid: "29687014"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Anslutningsbibliotek för Azure-databas för PostgreSQL
Den här artikeln innehåller bibliotek och drivrutiner som utvecklare kan använda för att utveckla program för att ansluta till och frågar Azure-databas för PostgreSQL.

## <a name="client-interfaces"></a>Klient-gränssnitt
De flesta språk klientbibliotek som används för att ansluta till PostgreSQL-servern är externa projekt och distribueras separat. Bibliotek i listan stöds på Windows, Linux och Mac-plattformar, för att ansluta till Azure-databas för PostgreSQL. Flera quickstart-exempel finns i avsnittet nästa steg.

| **Språk** | **Klientgränssnitt** | **Ytterligare information** | **Ladda ned** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB-API 2.0-kompatibel | [Ladda ned](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | Databas-tillägg | [Installera](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [PG npm-paket](https://www.npmjs.com/package/pg) | Ren JavaScript inte blockerar klienten | [Installera](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Typ 4 JDBC-drivrutinen | [Ladda ned](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG symbolen](https://deveiate.org/code/pg/) | Ruby-gränssnittet | [Ladda ned](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Paketet pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres driver | [Installera](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | ADO.NET Data Provider | [Ladda ned](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC-drivrutin | [Ladda ned](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primär C-användargränssnittet | Ingår |
| C++ | [libpqxx](http://pqxx.org/) | Ny formatmall C++-gränssnittet | [Ladda ned](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Nästa steg
Läs dessa Snabbstart att ansluta till och fråga Azure-databas för PostgreSQL med hjälp av ditt språk:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [gå](./connect-go.md)
