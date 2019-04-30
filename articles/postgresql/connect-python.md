---
title: Ansluta till Azure Database for PostgreSQL från Python
description: I den här snabbstarten finns ett kodexempel i Python som du kan använda för att ansluta till och fråga efter data från Azure Database för PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: eb6b23d8f8c476ba41bea918456fdf7fb9782920
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422176"
---
# <a name="azure-database-for-postgresql-use-python-to-connect-and-query-data"></a>Azure Database for PostgreSQL: Använda Python för att ansluta och fråga mot data
Den här snabbstarten visar hur du använder [Python](https://python.org) för att ansluta till en Azure Database för PostgreSQL. Den visar också hur SQL-instruktioner används för att fråga, infoga, uppdatera och ta bort data i databasen i macOS-, Ubuntu Linux- och Windows-plattformar. I den här artikeln förutsätter vi att du har kunskaper om Python och att du inte har arbetat med Azure Database för PostgreSQL tidigare.

## <a name="prerequisites"></a>Nödvändiga komponenter
I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa DB – Portal](quickstart-create-server-database-portal.md)
- [Skapa DB – CLI](quickstart-create-server-database-azure-cli.md)

Du behöver också:
- [Python](https://www.python.org/downloads/) installerad
- installera [PIP](https://pip.pypa.io/en/stable/installing/)-paketet (pip har redan installerats om du använder Python 2 > = 2.7.9 eller Python 3 > = 3-4-binärfiler som hämtats från [python.org](https://python.org)).

## <a name="install-the-python-connection-libraries-for-postgresql"></a>Installera Python-anslutningsbibliotek för PostgreSQL
Installera paketet [psycopg2](http://initd.org/psycopg/docs/install.html) för att ansluta till och fråga databasen. psycopg2 finns [i PyPI](https://pypi.python.org/pypi/psycopg2/) i form av [WHL](https://pythonwheels.com/)-paket för de flesta vanliga plattformar (Linux, OSX, Windows). Använd pip-installation för att få den binära versionen av modulen, inklusive alla beroenden.

1. Starta ett kommandoradsgränssnitt på din dator:
    - I Linux, starta Bash-gränssnittet.
    - I macOS, starta Terminal.
    - I Windows, starta kommandotolken från Start-menyn.
2. Se till att du använder den senaste versionen av pip genom att köra ett kommando som:
    ```cmd
    pip install -U pip
    ```

3. Kör följande kommando för att installera psycopg2-paketet:
    ```cmd
    pip install psycopg2
    ```

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Hämta den information som du behöver för att ansluta till Azure Database för PostgreSQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser**. Sök sedan efter den server som du skapade (till exempel **mydemoserver**).
3. Klicka på servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 ![Azure Database for PostgreSQL-servernamn](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-python-code"></a>Så här kör du Python-kod
Den här artikeln innehåller totalt fyra kodexempel som vart och ett utför en viss funktion. Följande anvisningar beskriver hur du skapar en textfil, infogar ett kodblock och sedan sparar filen så att du kan köra den senare. Se till att du skapar fyra olika filer, en för varje kodblock.

- Skapa en ny fil i valfri textredigerare.
- Kopiera och klistra in ett av kodexemplen i följande avsnitt i textfilen. Ersätt parametrarna **host**, **dbname**, **user** och **password** med de värden som du angav när du skapade servern och databasen.
- Spara filen med filnamnstillägget .py (till exempel postgres.py) i projektmappen. Om du kör Windows ska du se till att välja UTF-8-kodning när du sparar filen. 
- Starta kommandotolken, terminalen eller Bash-gränssnittet och byt katalog till din projektmapp, till exempel `cd postgres`.
-  Skriv Python-kommandot följt av filnamnet, till exempel `Python postgres.py`, för att köra koden.

> [!NOTE]
> Från och med Python version 3 kanske du ser felet `SyntaxError: Missing parentheses in call to 'print'` när du kör kodblocken nedan: Om detta händer, ersätter du varje anrop till kommandot  med ett funktionsanrop med parenteser, till exempel `print("string")`.

## <a name="connect-create-table-and-insert-data"></a>Ansluta, skapa tabell och infoga data
Använd följande kod för att ansluta och läsa in data med hjälp av funktionen [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) med en **INSERT**-SQL-instruktion. Funktionen [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) används för att köra SQL-frågor mot en PostgreSQL-databas. Ersätt parametrarna host, dbname,user och password med de värden som du angav när du skapade servern och databasen.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

När koden har körts visas utdata på följande sätt:

![Kommandoradsutdata](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Läsa data
Använd följande kod för att läsa data som infogats med funktionen [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) med **SELECT** SQL-instruktionen. Den här funktionen accepterar en fråga och returnerar en resultatuppsättning som kan upprepas med hjälp av [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). Ersätt parametrarna host, dbname,user och password med de värden som du angav när du skapade servern och databasen.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Uppdatera data
Med följande kod uppdaterar du inventarieraden du tidigare lade till med funktionen [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) och SQL-instruktionen **UPDATE** . Ersätt parametrarna host, dbname,user och password med de värden som du angav när du skapade servern och databasen.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Ta bort data
Med följande kod raderar du inventarieobjektet du tidigare lade till med funktionen [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) och SQL-instruktionen **DELETE** . Ersätt parametrarna host, dbname,user och password med de värden som du angav när du skapade servern och databasen.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print "Deleted 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
