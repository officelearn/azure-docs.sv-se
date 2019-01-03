---
title: Anslut program till Azure Database for MariaDB
description: Det här dokumentet innehåller för närvarande stöds anslutningssträngar att ansluta med Azure Database for MariaDB, inklusive ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python och Ruby-program.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 70cd25ff63101fa2a477cde2502d5d286b289366
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536582"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Så här ansluter du program till Azure Database for MariaDB
Det här avsnittet innehåller strängen anslutningstyper som stöds av Azure Database for MariaDB, tillsammans med mallar och exempel. Du kan ha olika parametrar och inställningar i anslutningssträngen.

- Certifikatet finns [hur du konfigurerar SSL](./howto-configure-ssl.md).
- {your_host} = [servername].mariadb.database.azure.com
- {your_user}@{servername} = användar-ID-format för autentisering på rätt sätt.  Om du bara använder användar-ID, misslyckas autentiseringen.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

I det här exemplet är servernamnet `mydemoserver`, databasnamnet är `wpdb`, användarnamnet är `WPAdmin`, och lösenordet är `mypassword!2`. Därför kan ska strängen som vara:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Hämta information om anslutningssträngen från Azure portal
I den [Azure-portalen](https://portal.azure.com), gå till din Azure Database for MariaDB-server och klicka sedan på **anslutningssträngar** att hämta stränglistan över för din instans: ![Fönstret anslutning strängar i Azure portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Strängen innehåller information som drivrutinen, server och andra databasen anslutningsparametrar. Ändra de här exemplen för att använda dina egna parametrar, till exempel databasens namn, lösenord och så vidare. Du kan sedan använda den här strängen för att ansluta till servern från din kod och dina program.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
