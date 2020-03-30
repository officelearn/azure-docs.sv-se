---
title: Anslut med omdirigering – Azure Database för MySQL
description: I den här artikeln beskrivs hur du kan konfigurera programmet för att ansluta till Azure Database för MySQL med omdirigering.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246343"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Ansluta till Azure Database för MySQL med omdirigering

I det här avsnittet beskrivs hur du ansluter ett program som din Azure-databas för MySQL-server med omdirigeringsläge. Omdirigering syftar till att minska nätverksfördröjningen mellan klientprogram och MySQL-servrar genom att tillåta program att ansluta direkt till servernoder för serverdelen.

## <a name="before-you-begin"></a>Innan du börjar
Logga in på [Azure-portalen](https://portal.azure.com). Skapa en Azure-databas för MySQL-server med motorversion 5.6, 5.7 eller 8.0. Mer information finns i [Så här skapar du Azure Database för MySQL-server från Portal](quickstart-create-mysql-server-database-using-azure-portal.md) eller Så här skapar du Azure Database för [MySQL-server med CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Omdirigering stöds för närvarande endast när **SSL är aktiverat** på din Azure-databas för MySQL-server. Mer information om hur du konfigurerar SSL finns i [Använda SSL med Azure Database för MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

Stöd för omdirigering i PHP-program är tillgängligt via [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) förlängning, som utvecklats av Microsoft. 

Den mysqlnd_azure förlängningen är tillgänglig för att lägga till PHP-program via PECL och det rekommenderas starkt att installera och konfigurera förlängningen genom den officiellt publicerade [PECL paketet](https://pecl.php.net/package/mysqlnd_azure).

> [!IMPORTANT]
> Stöd för omdirigering i PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) förlängning är för närvarande i förhandsgranskning.

### <a name="redirection-logic"></a>Omdirigeringslogik

>[!IMPORTANT]
> Omdirigeringslogik/-beteende som börjar version 1.1.0 uppdaterades och **det rekommenderas att version 1.1.0+ används.**

Omdirigeringsbeteendet bestäms `mysqlnd_azure.enableRedirect`av värdet för . I tabellen nedan beskrivs hur omdirigeringen har uppdämt baserat på värdet för den här parametern som börjar i **version 1.1.0+**.

Om du använder en äldre version av mysqlnd_azure-tillägget (version 1.0.0-1.0.3) bestäms omdirigeringsbeteendet av `mysqlnd_azure.enabled`värdet för . De giltiga `off` värdena är (fungerar på samma sätt som `on` det `preferred` beteende som beskrivs i tabellen nedan) och (fungerar som i tabellen nedan).  

|**mysqlnd_azure.enableRedirect-värde**| **Beteende**|
|----------------------------------------|-------------|
|`off` eller `0`|Omdirigering kommer inte att användas. |
|`on` eller `1`|- Om SSL inte är aktiverat på Azure Database för MySQL-servern görs ingen anslutning. Följande fel returneras: *"mysqlnd_azure.enableRedirect är aktiverat, men SSL-alternativet är inte inställt i anslutningssträngen. Omdirigering är endast möjlig med SSL."*<br>- Om SSL är aktiverat på MySQL-servern, men omdirigering inte stöds på servern, avbryts den första anslutningen och följande fel returneras: *"Anslutningen avbryts eftersom omdirigering inte är aktiverad på MySQL-servern eller nätverkspaketet inte uppfyller omdirigeringsprotokollet."*<br>- Om MySQL-servern stöder omdirigering, men den omdirigerade anslutningen misslyckades av någon anledning, avbryter även den första proxyanslutningen. Returnera felet för den omdirigerade anslutningen.|
|`preferred` eller `2`<br> (standardvärde)|- mysqlnd_azure kommer att använda omdirigering om möjligt.<br>- Om anslutningen inte använder SSL stöder servern inte omdirigering, eller omdirigeringsanslutningen misslyckas med att ansluta av någon icke-dödlig orsak medan proxyanslutningen fortfarande är giltig, kommer den att falla tillbaka till den första proxyanslutningen.|

De efterföljande avsnitten i dokumentet beskriver `mysqlnd_azure` hur du installerar tillägget med PECL och anger värdet för den här parametern.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Krav 
- PHP-versioner 7.2.15+ och 7.3.2+
- PHP PEAR 
- php-mysql
- Azure Database för MySQL-server med SSL aktiverat

1. Installera [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) med [PECL](https://pecl.php.net/package/mysqlnd_azure). Vi rekommenderar att du använder version 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Leta reda på`extension_dir`tilläggskatalogen ( ) genom att köra nedanstående:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Ändra kataloger till den returnerade mappen och se till att `mysqlnd_azure.so` den finns i den här mappen. 

4. Leta reda på mappen för .ini-filer genom att köra nedanstående: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Ändra kataloger till den här returnerade mappen. 

6. Skapa en ny INI-fil för `mysqlnd_azure`. Se till att alfabetet ordning namnet är efter mysqnld, eftersom modulerna laddas enligt namnordningen för ini-filer. Om `mysqlnd` till exempel .ini `10-mysqlnd.ini`heter namnges mysqlnd `20-mysqlnd-azure.ini`ini som .

7. Lägg till följande rader i den nya INI-filen för att aktivera omdirigering.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Krav 
- PHP-versioner 7.2.15+ och 7.3.2+
- php-mysql
- Azure Database för MySQL-server med SSL aktiverat

1. Ta reda på om du kör en x64- eller x86-version av PHP genom att köra följande kommando:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Ladda ner motsvarande x64 eller x86 version av [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL från [PECL](https://pecl.php.net/package/mysqlnd_azure) som matchar din version av PHP. Vi rekommenderar att du använder version 1.1.0+.

3. Extrahera zip-filen och hitta `php_mysqlnd_azure.dll`DLL med namnet .

4. Leta reda på`extension_dir`tilläggskatalogen ( ) genom att köra kommandot nedan:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Kopiera `php_mysqlnd_azure.dll` filen till katalogen som returneras i steg 4. 

6. Leta upp PHP-mappen som innehåller `php.ini` filen med följande kommando:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Ändra `php.ini` filen och lägg till följande extra rader för att aktivera omdirigering. 

    Under avsnittet Dynamiska tillägg: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Under avsnittet Modulinställningar:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Bekräfta omdirigering

Du kan också bekräfta omdirigering är konfigurerad med nedanstående exempel PHP-kod. Skapa en PHP-fil som heter `mysqlConnect.php` och klistra in nedanstående kod. Uppdatera servernamn, användarnamn och lösenord med ditt eget. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Nästa steg
Mer information om anslutningssträngar finns i [Anslutningssträngar](howto-connection-string.md).
