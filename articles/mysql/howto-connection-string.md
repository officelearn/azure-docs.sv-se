---
title: Anslut program till Azure Database för MySQL
description: Det här dokumentet innehåller för närvarande stöds anslutningssträngar att ansluta med Azure Database för MySQL, inklusive ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python och Ruby-program.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 06bd91adb0a86198f7709d0989624657ce00dfa9
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42060206"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Så här ansluter du program till Azure Database för MySQL
Det här avsnittet innehåller strängen anslutningstyper som stöds av Azure Database för MySQL, tillsammans med mallar och exempel. Du kan ha olika parametrar och inställningar i anslutningssträngen.

- Certifikatet finns [hur du konfigurerar SSL](./howto-configure-ssl.md).
- {your_host} = <servername>. mysql.database.azure.com
- {your_user}@{servername} = användar-ID-format för autentisering på rätt sätt.  Om du bara använder användar-ID, misslyckas autentiseringen.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

I det här exemplet är servernamnet `mydemoserver`, databasnamnet är `wpdb`, användarnamnet är `WPAdmin`, och lösenordet är `mypassword!2`. Därför kan ska strängen som vara:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Hämta information om anslutningssträngen från Azure portal
I den [Azure-portalen](https://portal.azure.com), gå till din Azure Database for MySQL-server och klicka sedan på **anslutningssträngar** att hämta stränglistan över för din instans: ![Connection strängar fönstret i Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Strängen innehåller information som drivrutinen, server och andra databasen anslutningsparametrar. Ändra de här exemplen för att använda dina egna parametrar, till exempel databasens namn, lösenord och så vidare. Du kan sedan använda den här strängen för att ansluta till servern från din kod och dina program.

## <a name="next-steps"></a>Nästa steg
- Läs mer om anslutningsbibliotek [begrepp - anslutningsbibliotek](./concepts-connection-libraries.md).
