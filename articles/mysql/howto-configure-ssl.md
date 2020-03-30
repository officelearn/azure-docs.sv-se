---
title: Konfigurera SSL - Azure-databas för MySQL
description: Instruktioner för hur du korrekt konfigurerar Azure Database för MySQL och associerade program för att korrekt använda SSL-anslutningar
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 557986560eb27beddf939bcab7f4d72a66781e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062438"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Konfigurera SSL-anslutning i ditt program för att ansluta till Azure Database för MySQL
Azure Database for MySQL stöder anslutning av din Azure Database for MySQL-server till klientprogram med SSL (Secure Sockets Layer). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.

## <a name="step-1-obtain-ssl-certificate"></a>Steg 1: Skaffa SSL-certifikat
Hämta certifikatet som behövs för att kommunicera via SSL [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) med din Azure-databas för MySQL-server från och spara certifikatfilen på din lokala enhet (den här självstudien använder c:\ssl till exempel).
**För Microsoft Internet Explorer och Microsoft Edge:** När nedladdningen har slutförts byter du namn på certifikatet till BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Steg 2: Bind SSL

För specifika kopplingssträngar för programmeringsspråk, se [exempelkoden](howto-configure-ssl.md#sample-code) nedan.

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Ansluta till servern med MySQL Workbench via SSL
Konfigurera MySQL Workbench så att den kan anslutas säkert via SSL. 

1. Gå till fliken **Konfigurera** ny anslutning från dialogrutan Ny anslutning. 

1. Uppdatera fältet **Använd SSL** för att "Kräv".

1. I fältet **SSL CA::** anger du filplatsen för **BaltimoreCyberTrustRoot.crt.pem**. 
    
    ![Spara SSL-konfiguration](./media/howto-configure-ssl/mysql-workbench-ssl.png)

För befintliga anslutningar kan du binda SSL genom att högerklicka på anslutningsikonen och välja redigera. Navigera sedan till **fliken SSL** och bind cert-filen.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Ansluta till servern med MySQL CLI via SSL
Ett annat sätt att binda SSL-certifikatet är att använda MySQL-kommandoradsgränssnittet genom att köra följande kommandon. 

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> När du använder MySQL-kommandoradsgränssnittet i `SSL connection error: Certificate signature check failed`Windows kan ett felmeddelande visas . Om detta inträffar `--ssl-mode=REQUIRED --ssl-ca={filepath}` ersätter du parametrarna med `--ssl`.

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Steg 3: Tvinga fram SSL-anslutningar i Azure 
### <a name="using-the-azure-portal"></a>Använda Azure Portal
Besök Azure-databasen för MySQL-server med Azure-portalen och klicka sedan på **Anslutningssäkerhet**. Använd växlingsknappen för att aktivera eller inaktivera inställningen **Framtvinga SSL-anslutning** och klicka sedan på **Spara**. Microsoft rekommenderar att du alltid aktiverar inställningen **Framtvinga SSL-anslutning** för ökad säkerhet.
![aktivera-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Använda Azure CLI
Du kan aktivera eller inaktivera **parametern ssl-enforcement** genom att använda Aktiverade respektive inaktiverade värden i Azure CLI.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Steg 4: Verifiera SSL-anslutningen
Kör kommandot mysql **status** för att kontrollera att du har anslutit till MySQL-servern med SSL:
```dos
mysql> status
```
Bekräfta att anslutningen är krypterad genom att granska utdata, som ska visa: **SSL: Chiffer som används är AES256-SHA** 

## <a name="sample-code"></a>Exempelkod
Om du vill upprätta en säker anslutning till Azure Database for MySQL via SSL från ditt program läser du följande kodexempel:

Se listan över [kompatibla drivrutiner](concepts-compatibility.md) som stöds av Azure Database for MySQL-tjänsten.

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="php-using-pdo"></a>PHP (Använda SUB)
```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

### <a name="django-pymysql"></a>Django (PyMysql)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
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
### <a name="java-mysql-connector-for-java"></a>Java (MySQL-kontakt för Java)
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
### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB-kontakt för Java)
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
Granska olika programanslutningsalternativ efter [anslutningsbibliotek för Azure Database för MySQL](concepts-connection-libraries.md)
