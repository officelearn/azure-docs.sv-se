---
title: "Konfigurera SSL-anslutning för att ansluta säkert till Azure-databas för MySQL | Microsoft Docs"
description: "Instruktioner för hur du ska konfigurera Azure-databas för MySQL och associerade program använder SSL-anslutningar"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 289d1c4c0ffd2667c49c5625e72780d54a71ceb5
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Konfigurera SSL-anslutning i ditt program för att ansluta säkert till Azure-databas för MySQL
Azure-databas för MySQL stöder anslutning av din Azure-databas för MySQL-servern till klientprogram som använder Secure Sockets Layer (SSL). Framtvinga SSL-anslutningar mellan databasservern och ditt klientprogram hjälper dig att skydda mot attacker som ”man i mitten” genom att kryptera dataströmmen mellan servern och ditt program.

## <a name="step-1-obtain-ssl-certificate"></a>Steg 1: Få SSL-certifikat
Hämta certifikat som behövs för att kommunicera via SSL med din Azure-databas för MySQL-servern från [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) och spara filen med certifikat till din lokala enhet (detta kursen använder c:\ssl till exempel).
**För Microsoft Internet Explorer och Microsoft Edge:** när hämtningen är slutförd, ändra certifikatet till BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Steg 2: Binda SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Ansluta till servern med hjälp av MySQL-arbetsstationen via SSL
Konfigurera MySQL-arbetsstationen för att ansluta på ett säkert sätt via SSL. Konfigurera nya Connection dialog, navigerar du till den **SSL** fliken. I den **SSL CA-fil:** , ange sökvägen till den **BaltimoreCyberTrustRoot.crt.pem**. 
![Spara anpassade panelen](./media/howto-configure-ssl/mysql-workbench-ssl.png) för befintliga anslutningar kan du binda SSL genom att högerklicka på ikonen och välj Redigera. Gå sedan till den **SSL** fliken och binda av certifikatfilen.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Ansluter till servern med hjälp av MySQL-CLI via SSL
Ett annat sätt att binda SSL-certifikatet är att använda MySQL-kommandoradsgränssnittet genom att köra följande kommando:
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Steg 3: Framtvinga SSL-anslutningar i Azure 
### <a name="using-the-azure-portal"></a>Använda Azure Portal
Med hjälp av Azure portal finns din Azure-databas för MySQL-servern och klickar sedan på **anslutningssäkerhet**. Använd växlingsknappen för att aktivera eller inaktivera den **framtvinga SSL-anslutning** inställningen och klicka sedan på **spara**. Microsoft rekommenderar för att aktivera alltid den **framtvinga SSL-anslutning** för ökad säkerhet.
![Aktivera ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Använda Azure CLI
Du kan aktivera eller inaktivera den **ssl tvingande** parameter med aktiverad eller inaktiverad värden respektive i Azure CLI.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Steg 4: Verifiera SSL-anslutning
Köra mysql **status** kommando för att kontrollera att du har anslutit till MySQL-servern med hjälp av SSL:
```dos
mysql> status
```
Bekräfta anslutningen är krypterad genom att granska utdata som ska visas: **SSL: Cipher används är AES256 SHA** 

## <a name="sample-code"></a>Exempelkod
För att upprätta en säker anslutning till Azure-databas för MySQL via SSL från ditt program, se följande kodexempel:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@myserver4demo', 
        password='yourpassword', 
        database='quickstartdb', 
        host='myserver4demo.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@myserver4demo', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'myserver4demo.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'myserver4demo.mysql.database.azure.com', 
        :username => 'myadmin@myserver4demo',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool, InsecureSkipVerify: true})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@myserver4demo' , 'yourpassword', 'myserver4demo.mysql.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="javajdbc"></a>JAVA(JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'myserver4demo.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@myserver4demo');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="javamariadb"></a>Java(MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'myserver4demo.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@myserver4demo');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

## <a name="next-steps"></a>Nästa steg
Granska olika anslutningsalternativ för programmet efter [anslutningsbibliotek för Azure-databas för MySQL](concepts-connection-libraries.md)
