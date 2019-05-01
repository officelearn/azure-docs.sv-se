---
title: Konfigurera SSL-anslutning för säker anslutning till Azure Database for MySQL
description: Instruktioner för hur du ska konfigurera Azure Database för MySQL och associerade program använder SSL-anslutningar
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 0eec75767ab61aef9322e7475515871bdd36b5d4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572400"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Konfigurera SSL-anslutning i din app för säker anslutning till Azure Database for MySQL
Azure Database för MySQL stöder anslutning av din Azure Database for MySQL-server till klientprogram som använder Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.

## <a name="step-1-obtain-ssl-certificate"></a>Steg 1: Skaffa SSL-certifikat
Hämta det certifikat som krävs för kommunikation via SSL med din Azure Database for MySQL-server från [ https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem ](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) och spara certifikatfilen på den lokala enheten (den här självstudien använder c:\ssl till exempel).
**För Microsoft Internet Explorer och Microsoft Edge:** När hämtningen har slutförts kan du ändra certifikatet till BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Steg 2: SSL-bindning
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Ansluta till servern med MySQL Workbench via SSL
Konfigurera MySQL-arbetsstationen för att ansluta på ett säkert sätt via SSL. Konfigurera ny anslutning dialog, navigera till den **SSL** fliken. I den **SSL CA-fil:** fältet, anger du sökvägen till den **BaltimoreCyberTrustRoot.crt.pem**. 
![Spara anpassade panel](./media/howto-configure-ssl/mysql-workbench-ssl.png) för befintliga anslutningar kan du binda SSL genom att högerklicka på ikonen för anslutningen och välj Redigera. Gå sedan till den **SSL** fliken och bind certifikat-fil.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Ansluta till servern med hjälp av CLI MySQL via SSL
Ett annat sätt att binda SSL-certifikat är att använda kommandoradsgränssnittet MySQL genom att köra följande kommandon. 

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> När du använder gränssnittet för MySQL-kommandorad i Windows, kan ett felmeddelande `SSL connection error: Certificate signature check failed`. Om detta inträffar, ersätter den `--ssl-mode=REQUIRED --ssl-ca={filepath}` parametrar med `--ssl`.

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Steg 3:  Att framtvinga SSL-anslutningar i Azure 
### <a name="using-the-azure-portal"></a>Använda Azure Portal
Med Azure-portalen, gå till din Azure Database for MySQL-server och klicka sedan på **anslutningssäkerhet**. Använd växlingsknappen för att aktivera eller inaktivera den **framtvinga SSL-anslutning** inställningen och klicka sedan på **spara**. Microsoft rekommenderar att du alltid lägga till den **framtvinga SSL-anslutning** för förbättrad säkerhet.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Använda Azure CLI
Du kan aktivera eller inaktivera den **ssl tvingande** parameter med hjälp av aktiverad eller inaktiverad, värden respektive i Azure CLI.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Steg 4: Verifiera SSL-anslutning
Kör mysql **status** kommandot för att kontrollera att du har anslutit till din MySQL-server med hjälp av SSL:
```dos
mysql> status
```
Bekräfta anslutningen krypteras genom att granska utdata som ska visa:  **SSL: Cipher används är AES256 SHA** 

## <a name="sample-code"></a>Exempelkod
Om du vill upprätta en säker anslutning till Azure Database for MySQL via SSL från ditt program, se följande kodexempel:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
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
        host='mydemoserver.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com', 
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
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="javamariadb"></a>JAVA(MariaDB)
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Nästa steg
Granska olika anslutningsalternativ för programmet efter [anslutningsbibliotek för Azure Database for MySQL](concepts-connection-libraries.md)
