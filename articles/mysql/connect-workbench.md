---
title: "Ansluta till Azure Database for MySQL från MySQL Workbench | Microsoft Docs"
description: "Den här snabbstarten förklarar hur du använder MySQL Workbench för att ansluta till och fråga efter data från Azure Database for MySQL."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: seanli1988
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 4214b80dc4c5db644d5a05f942907c86f292fe18
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="azure-database-for-mysql-use-mysql-workbench-to-connect-and-query-data"></a>Azure Database for MySQL: Använda MySQL Workbench för att ansluta och fråga efter data
Den här snabbstarten visar hur du ansluter till en Azure Database for MySQL med hjälp av ett MySQL Workbench-program. 

## <a name="prerequisites"></a>Nödvändiga komponenter
I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa en Azure Database för MySQL med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>Installera MySQL Workbench
Hämta och installera MySQL Workbench på datorn från [webbplatsen för MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Skaffa den information som du behöver för att ansluta till Azure Database för MySQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser** och söker efter den server som du nyss skapade (till exempel **myserver4demo**).

3. Klicka på servernamnet.

4. Välj sidan **Egenskaper** för servern och notera **Servernamn** och **Inloggningsnamn för serveradministratören**.

 ![Azure Database for MySQL-servernamn](./media/connect-workbench/1-server-properties-name-login.png)
 
5. Om du glömmer inloggningsinformationen för servern öppnar du sidan **Översikt** för att se inloggningsnamnet för serveradministratören. Om det behövs kan du återställa lösenordet.

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Ansluta till servern med MySQL Workbench 
Ansluta till Azure MySQL-servern med GUI-verktyget MySQL Workbench:

1.  Starta programmet MySQL Workbench på datorn. 

2.  I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

    ![konfigurera ny anslutning](./media/connect-workbench/2-setup-new-connection.png)

    | **Inställning** | **Föreslaget värde** | **Fältbeskrivning** |
    |---|---|---|
    |   Anslutningsnamn | Demoanslutning | Ange ett namn på anslutningen. |
    | Anslutningsmetod | Standard (TCP/IP) | Standard (TCP/IP) är tillräckligt. |
    | Värdnamn | *servernamn* | Ange det värde för servernamn som användes när du tidigare skapade Azure Database för MySQL. Exempelservern som visas är myserver4demo.mysql.database.azure.com. Använd det fullständiga domännamnet (\*.mysql.database.azure.com) som i det här exemplet. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg namnet på servern.  |
    | Port | 3306 | Använd alltid port 3306 när du ansluter till Azure Database för MySQL. |
    | Användarnamn |  *inloggning för serveradministratör* | Ange det användarnamn för serveradministratörsinloggning som användes när du tidigare skapade Azure Database för MySQL. Vår användarnamn i exemplet är myadmin@myserver4demo. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg användarnamnet. Formatet är *username@servername*.
    | Lösenord | ditt lösenord | Klicka på knappen **Spara i valvet...** för att spara lösenordet. |

3.   Klicka på **Testanslutning** för att testa om alla parametrar är rätt konfigurerade. 

4.   Spara anslutningen genom att klicka på **OK**. 

5.   I listan **MySQL-anslutningar** klickar du på panelen som motsvarar din server och väntar sedan tills anslutningen har upprättats.

        En ny SQL-flik öppnas med en tom redigerare där du kan ange dina frågor.
    
        > [!NOTE]
        > SSL-anslutningssäkerhet krävs som standard och framtvingas på Azure Database for MySQL-servern. Men vanligtvis krävs ingen ytterligare konfiguration med SSL-certifikat för att MySQL Workbench ska ansluta till servern. Vi rekommenderar att du binder ihop SSL CA-certifieringen med MySQL Workbench. Se [Konfigurera SSL-anslutning i din app för säker anslutning till Azure Database for MySQL](./howto-configure-ssl.md) för mer information om hur du laddar ned och binder certifieringen.  Om du behöver inaktivera SSL öppnar du Azure Portal, klickar på sidan Anslutningssäkerhet och inaktiverar alternativet Framtvinga SSL-anslutning.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Skapa en tabell, infoga data, läsa data, uppdatera data, ta bort data
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

Du har nu anslutit till Azure Database for MySQL med MySQL Workbench, och du har frågat data med hjälp av SQL-språket.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./concepts-migrate-import-export.md)
