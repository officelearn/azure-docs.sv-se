---
title: Anslutningssträngar - Azure Database för MariaDB
description: I det här dokumentet visas de anslutningssträngar som för närvarande stöds för program för att ansluta till Azure Database för MariaDB, inklusive ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python och Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 74574fb905ae4ebd2552ef97cd0b5430eea6363a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530231"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Så här ansluter du program till Azure Database for MariaDB
I det här avsnittet visas de anslutningssträngtyper som stöds av Azure Database för MariaDB, tillsammans med mallar och exempel. Du kan ha olika parametrar och inställningar i anslutningssträngen.

- Information om hur du hämtar certifikatet finns i [Så här konfigurerar du SSL](./howto-configure-ssl.md).
- {your_host} = [servernamn].mariadb.database.azure.com
- {your_user}@{servername} = userID-format för korrekt autentisering.  Om du bara använder userID misslyckas autentiseringen.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

I det här exemplet `mydemoserver`är servernamnet `wpdb`, databasnamnet `WPAdmin`är , användarnamnet är och lösenordet är `mypassword!2`. Därför bör anslutningssträngen vara:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Hämta information om anslutningssträngen från Azure-portalen
I [Azure-portalen](https://portal.azure.com)går du till din Azure-databas för MariaDB-server och klickar ![sedan på **Anslutningssträngar** för att hämta stränglistan för din instans: fönstret Anslutningssträngar i Azure-portalen](./media/howto-connection-strings/connection-strings-on-portal.png)

Strängen innehåller information som drivrutin, server och andra databasanslutningsparametrar. Ändra de här exemplen om du vill använda egna parametrar, till exempel databasnamn, lösenord och så vidare. Du kan sedan använda den här strängen för att ansluta till servern från din kod och dina program.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
