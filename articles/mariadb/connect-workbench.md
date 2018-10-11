---
title: Ansluta till Azure Database for MariaDB från MySQL Workbench
description: Den här snabbstarten förklarar hur du använder MySQL Workbench för att ansluta till och fråga efter data från Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: b3a4d00381361b5299e86b959d9775318ae81e88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998261"
---
# <a name="azure-database-for-mariadb-use-mysql-workbench-to-connect-and-query-data"></a>Azure Database for MariaDB: Använda MySQL Workbench för att ansluta och fråga efter data
Den här snabbstarten visar hur du ansluter till en Azure Database for MariaDB med hjälp av ett MySQL Workbench-program. 

## <a name="prerequisites"></a>Nödvändiga komponenter
I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa en Azure Database for MariaDB-server med Azure-portalen](./quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Skapa en Azure Database for MariaDB-server med Azure CLI](./quickstart-create-mariadb-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>Installera MySQL Workbench
Hämta och installera MySQL Workbench på datorn från [webbplatsen för MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Skaffa den information som du behöver för att ansluta till Azure Database för MariaDB. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser**. Sök sedan efter den server som du skapade (till exempel **mydemoserver**).

3. Klicka på servernamnet.

4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 ![Azure Database for MariaDB-servernamn](./media/connect-workbench/1_server-overview-name-login.png)

## <a name="connect-to-server-using-mysql-workbench"></a>Ansluta till server med MySQL Workbench 
Använd följande format för att ansluta till en Azure Database for MariaDB-server med MySQL Workbench gör du följande:

1.  Starta programmet MySQL Workbench på datorn. 

2.  I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

    ![konfigurera ny anslutning](./media/connect-workbench/2-setup-new-connection.png)

    | **Inställning** | **Föreslaget värde** | **Fältbeskrivning** |
    |---|---|---|
    |   Anslutningsnamn | Demoanslutning | Ange ett namn på anslutningen. |
    | Anslutningsmetod | Standard (TCP/IP) | Standard (TCP/IP) är tillräckligt. |
    | Värdnamn | *servernamn* | Ange det värde för servernamn som användes när du tidigare skapade Azure Database för MariaDB. Exempelservern som visas är mydemoserver.mariadb.database.azure.com. Använd det fullständiga domännamnet (\*.mariadb.database.azure.com) som i det här exemplet. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg namnet på servern.  |
    | Port | 3306 | Använd alltid port 3306 när du ansluter till Azure Database för MariaDB. |
    | Användarnamn |  *inloggning för serveradministratör* | Ange det användarnamn för serveradministratörsinloggning som användes när du tidigare skapade Azure Database för MariaDB. Vår användarnamn i exemplet är myadmin@mydemoserver. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg användarnamnet. Formatet är *username@servername*.
    | Lösenord | ditt lösenord | Klicka på knappen **Spara i valvet...** för att spara lösenordet. |

3.   Klicka på **Testanslutning** för att testa om alla parametrar är rätt konfigurerade. 

4.   Spara anslutningen genom att klicka på **OK**. 

5.   I listan **MySQL-anslutningar** klickar du på panelen som motsvarar din server och väntar sedan tills anslutningen har upprättats.

        En ny SQL-flik öppnas med en tom redigerare där du kan ange dina frågor.
    
        > [!NOTE]
        > SSL-anslutningssäkerhet krävs som standard och framtvingas på Azure Database for MariaDB-servern. Men vanligtvis krävs ingen ytterligare konfiguration med SSL-certifikat för att MySQL Workbench ska ansluta till servern. Vi rekommenderar att du binder ihop SSL CA-certifieringen med MySQL Workbench. Om du behöver inaktivera SSL öppnar du Azure Portal, klickar på sidan Anslutningssäkerhet och inaktiverar alternativet Framtvinga SSL-anslutning.

## <a name="create-table-insert-read-update-and-delete-data"></a>Skapa tabell, infoga, läsa, uppdatera och ta bort data
1. Kopiera och klistra in SQL-exempelkoden på en tom SQL-flik för att visa exempeldata.

    Den här koden skapar en tom databas med namnet quickstartdb och skapar sedan en exempeltabell med namnet inventory. Den infogar vissa rader och läser sedan raderna. Den ändrar data med en update-instruktion och läser raderna igen. Den tar slutligen bort en rad och läser sedan raderna igen.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    Skärmbilden visar ett exempel på SQL-koden i SQL Workbench och utdata efter att den har körts.
    
    ![Fliken SQL i MySQL Workbench för att köra SQL-exempelkod](media/connect-workbench/3-workbench-sql-tab.png)

2. Om du vill köra SQL-exempelkoden klickar du på blixtikonen i verktygsfältet på fliken **SQL-fil**.
3. Lägg märke till de tre resultaten med flikar i avsnittet med **rutnätet med resultat** mitt på sidan. 
4. Lägg märke till listan **Output** (Utdata) längst ned på sidan. Status för varje kommando visas. 

Du har nu anslutit till Azure Database for MariaDB med MySQL Workbench, och du har frågat data med hjälp av SQL-språket.

<!--
## Next steps
> [!div class="nextstepaction"]
> [Migrate your database using Export and Import](./concepts-migrate-import-export.md)
-->