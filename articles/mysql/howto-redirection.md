---
title: Anslut till Azure Database for MySQL med omdirigering
description: I den här artikeln beskrivs hur du kan konfigurera programmet för att ansluta till Azure Database for MySQL med omdirigering.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233745"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Anslut till Azure Database for MySQL med omdirigering

I det här avsnittet beskrivs hur du ansluter ett program till din Azure Database for MySQL-server med omdirigeringsläge. Omdirigering syftar till att minska nätverks fördröjningen mellan klient program och MySQL-servrar genom att tillåta att program ansluter direkt till backend-serverns noder.

> [!IMPORTANT]
> Stöd för omdirigering i PHP- [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) är för närvarande en för hands version.

## <a name="before-you-begin"></a>Innan du börjar
Logga in på [Azure-portalen](https://portal.azure.com). Skapa en Azure Database for MySQL-server med motor version 5,6, 5,7 eller 8,0. Mer information finns i så här [skapar du Azure Database for MySQL server från portalen](quickstart-create-mysql-server-database-using-azure-portal.md) eller [hur du skapar Azure Database for MySQL server med CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Omdirigering stöds för närvarande bara när SSL är aktiverat. Mer information om hur du konfigurerar SSL finns i [använda SSL med Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure). 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Förutsättningar 
- PHP-versioner 7.2.15 + och 7.3.2 +
- PHP PÄRON 
- php – mysql
- Azure Database for MySQL server med SSL aktiverat

1. Installera [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) med [PECL](https://pecl.php.net/package/mysqlnd_azure).

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Leta upp tilläggs katalogen (`extension_dir`) genom att köra följande:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Ändra kataloger till den returnerade mappen och se till att `mysqlnd_azure.so` finns i den här mappen. 

4. Leta upp mappen för INI-filer genom att köra följande: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Ändra kataloger till den här returnerade mappen. 

6. Skapa en ny ini-fil för `mysqlnd_azure`. Se till att bokstavs ordningen för namnet är efter det att mysqnld, eftersom modulerna har lästs in enligt namn ordningen för ini-filerna. Om `mysqlnd`. ini till exempel heter `10-mysqlnd.ini`, namnge mysqlnd ini som `20-mysqlnd-azure.ini`.

7. I den nya ini-filen lägger du till följande rader för att aktivera omdirigering.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Förutsättningar 
- PHP-versioner 7.2.15 + och 7.3.2 +
- php – mysql
- Azure Database for MySQL server med SSL aktiverat

1. Ta reda på om du kör en x64-eller x86-version av PHP genom att köra följande kommando:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Hämta motsvarande x64-eller x86-version av [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -DLL-filen från [PECL](https://pecl.php.net/package/mysqlnd_azure) som matchar din version av php. 

3. Extrahera zip-filen och hitta DLL-filen som heter `php_mysqlnd_azure.dll`.

4. Leta upp tilläggs katalogen (`extension_dir`) genom att köra kommandot nedan:

    ```cmd
    php -i | find "extension_dir"s
    ```

5. Kopiera `php_mysqlnd_azure.dll`-filen till den katalog som returnerades i steg 4. 

6. Leta upp PHP-mappen som innehåller `php.ini`-filen med hjälp av följande kommando:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Ändra `php.ini`-filen och Lägg till följande extra rader för att aktivera omdirigering. 

    Under avsnittet dynamiska tillägg: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Under avsnittet Modulnamn:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>Bekräfta omdirigering

Du kan också bekräfta att omdirigering har kon figurer ATS med nedanstående exempel-PHP-kod. Skapa en PHP-fil med namnet `mysqlConnect.php` och klistra in nedanstående kod. Uppdatera Server namnet, användar namnet och lösen ordet med ditt eget. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
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
Mer information om anslutnings strängar finns i [anslutnings strängar](howto-connection-string.md).

