---
title: Konfigurera SSL-anslutning för säker anslutning till Azure Database for MariaDB
description: Instruktioner för hur du ska konfigurera Azure Database for MariaDB och associerade program använder SSL-anslutningar
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4bf18a44255903df09aae3382c0eb35a2a55eea5
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541820"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Konfigurera SSL-anslutning i ditt program för säker anslutning till Azure Database for MariaDB
Azure Database for MariaDB stöder anslutning av din Azure Database for MariaDB-server till klientprogram som använder Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.

## <a name="obtain-ssl-certificate"></a>Skaffa SSL-certifikat
Hämta det certifikat som krävs för kommunikation via SSL med din Azure Database for MariaDB-servern från [ https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem ](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) och spara certifikatfilen på den lokala enheten (den här självstudien använder c:\ssl till exempel).
**För Microsoft Internet Explorer och Microsoft Edge:** När hämtningen har slutförts kan du ändra certifikatet till BaltimoreCyberTrustRoot.crt.pem.

## <a name="bind-ssl"></a>SSL-bindning
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Ansluta till servern med MySQL Workbench via SSL
Konfigurera MySQL-arbetsstationen för att ansluta på ett säkert sätt via SSL. Konfigurera ny anslutning dialog, navigera till den **SSL** fliken. I den **SSL CA-fil:** fältet, anger du sökvägen till den **BaltimoreCyberTrustRoot.crt.pem**. 
![Spara anpassade panel](./media/howto-configure-ssl/mysql-workbench-ssl.png) för befintliga anslutningar kan du binda SSL genom att högerklicka på ikonen för anslutningen och välj Redigera. Gå sedan till den **SSL** fliken och bind certifikat-fil.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Ansluta till servern med hjälp av CLI MySQL via SSL
Ett annat sätt att binda SSL-certifikat är att använda kommandoradsgränssnittet MySQL genom att köra följande kommando:
```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="enforcing-ssl-connections-in-azure"></a>Att framtvinga SSL-anslutningar i Azure 
### <a name="using-the-azure-portal"></a>Använda Azure Portal
Med Azure-portalen, gå till din Azure Database for MariaDB-server och klicka sedan på **anslutningssäkerhet**. Använd växlingsknappen för att aktivera eller inaktivera den **framtvinga SSL-anslutning** inställningen och klicka sedan på **spara**. Microsoft rekommenderar att du alltid lägga till den **framtvinga SSL-anslutning** för förbättrad säkerhet.
![Aktivera ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Använda Azure CLI
Du kan aktivera eller inaktivera den **ssl tvingande** parameter med hjälp av aktiverad eller inaktiverad, värden respektive i Azure CLI.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Verifiera SSL-anslutning
Kör mysql **status** kommandot för att kontrollera att du har anslutit till MariaDB-servern med hjälp av SSL:
```sql
status
```
Bekräfta anslutningen krypteras genom att granska utdata som ska visa:  **SSL: Cipher används är AES256 SHA** 

## <a name="sample-code"></a>Exempelkod
För att upprätta en säker anslutning till Azure Database for MariaDB över SSL från ditt program, se följande kodexempel:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@mydemoserver', 
        password='yourpassword', 
        database='quickstartdb', 
        host='mydemoserver.mariadb.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mariadb.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
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
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
