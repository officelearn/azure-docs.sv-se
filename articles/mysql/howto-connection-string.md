---
title: Anslutningssträngar - Azure Database för MySQL
description: I det här dokumentet visas de anslutningssträngar som för närvarande stöds för program för att ansluta till Azure Database for MySQL, inklusive ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python och Ruby.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ff257e101ca925a554f2343d357e2042d2930b45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062399"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Så ansluter du program till Azure Database for MySQL
I det här avsnittet visas de anslutningssträngtyper som stöds av Azure Database for MySQL, tillsammans med mallar och exempel. Du kan ha olika parametrar och inställningar i anslutningssträngen.

- Information om hur du hämtar certifikatet finns i [Så här konfigurerar du SSL](./howto-configure-ssl.md).
- {your_host} = \<servernamn>.mysql.database.azure.com
- {your_user}@{servername} = userID-format för korrekt autentisering.  Om du bara använder userID misslyckas autentiseringen.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

I det här exemplet `mydemoserver`är servernamnet `wpdb`, databasnamnet `WPAdmin`är , användarnamnet är och lösenordet är `mypassword!2`. Därför bör anslutningssträngen vara:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Hämta information om anslutningssträngen från Azure-portalen
I [Azure-portalen](https://portal.azure.com)går du till din Azure Database for MySQL-server och klickar ![sedan på **Anslutningssträngar** för att hämta stränglistan för din instans: fönstret Anslutningssträngar i Azure-portalen](./media/howto-connection-strings/connection-strings-on-portal.png)

Strängen innehåller information som drivrutin, server och andra databasanslutningsparametrar. Ändra de här exemplen om du vill använda egna parametrar, till exempel databasnamn, lösenord och så vidare. Du kan sedan använda den här strängen för att ansluta till servern från din kod och dina program.

## <a name="next-steps"></a>Nästa steg
- Mer information om anslutningsbibliotek finns i [Begrepp - Anslutningsbibliotek](./concepts-connection-libraries.md).
