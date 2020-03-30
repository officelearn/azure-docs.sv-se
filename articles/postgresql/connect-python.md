---
title: Anslut med Python - Azure Database för PostgreSQL - Single Server
description: Den här snabbstarten innehåller Python-kodexempel som du kan använda för att ansluta och fråga data från Azure Database för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 3694c0b74393068538a0c8f496444a1541d88fee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76769073"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Snabbstart: Använda Python för att ansluta och fråga data i Azure Database för PostgreSQL - Single Server

I den här snabbstarten arbetar du med en Azure-databas för PostgreSQL med Python på macOS, Ubuntu Linux eller Windows. Snabbstarten visar hur du ansluter till databasen och använder SQL-uttryck för att fråga, infoga, uppdatera och ta bort data. Artikeln förutsätter att du är bekant med Python, men ny på att arbeta med Azure Database för PostgreSQL.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Slutförande av [snabbstart: Skapa en Azure-databas för PostgreSQL-server i Azure-portalen](quickstart-create-server-database-portal.md) eller [Snabbstart: Skapa en Azure-databas för PostgreSQL med Hjälp av Azure CLI](quickstart-create-server-database-azure-cli.md).
  
- [Python](https://www.python.org/downloads/) 2.7.9+ eller 3.4+.
  
- Senaste [pip](https://pip.pypa.io/en/stable/installing/) paketinstallationsprogrammet.

## <a name="install-the-python-libraries-for-postgresql"></a>Installera Python-biblioteken för PostgreSQL
[Psycopg2-modulen](https://pypi.python.org/pypi/psycopg2/) gör det möjligt att ansluta till och fråga en PostgreSQL-databas och finns som ett Linux-, macOS- eller [Windows-hjulpaket.](https://pythonwheels.com/) Installera den binära versionen av modulen, inklusive alla beroenden. Mer information `psycopg2` om installation och krav finns i [Installation](http://initd.org/psycopg/docs/install.html). 

Om `psycopg2`du vill installera öppnar du en `pip install psycopg2`terminal eller kommandotolk och kör kommandot .

## <a name="get-database-connection-information"></a>Hämta information om databasanslutning
För anslutning till en Azure-databas för PostgreSQL-databas krävs fullständigt kvalificerat servernamn och inloggningsuppgifter. Du kan hämta den här informationen från Azure-portalen.

1. Sök [Azure portal](https://portal.azure.com/)efter och välj azure-databas för PostgreSQL-servernamn i Azure-portalen. 
1. Kopiera det fullständigt kvalificerade **servernamnet** och **administratörsanvändarnamnet**på sidan **Översikt.** Det fullständigt kvalificerade **servernamnet** är alltid av formuläret * \<my-server-name>.postgres.database.azure.com*, och **administratörsanvändarnamnet** är alltid av formuläret * \<my-admin-username>@\<my-server-name>*. 
   
   Du behöver också ditt administratörslösenord. Om du glömmer det kan du återställa det från den här sidan. 
   
   ![Azure Database for PostgreSQL-servernamn](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>Så här kör du Python-exemplen

För varje kodexempel i den här artikeln:

1. Skapa en ny fil i en textredigerare. 
   
1. Lägg till kodexemplet i filen. I koden ersätter du:
   - `<server-name>`och `<admin-username>` med de värden som du kopierade från Azure-portalen.
   - `<admin-password>`med ditt serverlösenord.
   - `<database-name>`med namnet på din Azure-databas för PostgreSQL-databas. En standarddatabas med namnet *postgres* skapades automatiskt när du skapade servern. Du kan byta namn på databasen eller skapa en ny databas med hjälp av SQL-kommandon. 
   
1. Spara filen i projektmappen med ett *py-tillägg,* till exempel *postgres-insert.py*. För Windows kontrollerar du att UTF-8-kodningen är markerad när du sparar filen. 
   
1. Om du vill köra filen ändrar du till projektmappen i ett kommandoradsgränssnitt och skriver `python` följt av filnamnet, till exempel `python postgres-insert.py`.

## <a name="create-a-table-and-insert-data"></a>Skapa en tabell och infoga data
Följande kodexempel ansluter till din Azure-databas för PostgreSQL-databas med funktionen [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) och läser in data med ett SQL **INSERT-uttryck.** Funktionen [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) kör SQL-frågan mot databasen. 

```Python
import psycopg2

# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

När koden körs korrekt, den ger följande utdata:

![Kommandoradsutdata](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Läsa data
Följande kodexempel ansluter till din Azure-databas för PostgreSQL-databas och använder [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) med SQL **SELECT-uttrycket** för att läsa data. Den här funktionen accepterar en fråga och returnerar en resultatuppsättning för att iterera över med hjälp av [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Uppdatera data
Följande kodexempel ansluter till din Azure-databas för PostgreSQL-databas och använder [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) med SQL **UPDATE-uttrycket** för att uppdatera data. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Ta bort data
Följande kodexempel ansluter till din Azure-databas för PostgreSQL-databas och använder [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) med SQL **DELETE-satsen** för att ta bort en lagerartikel som du tidigare infogat. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
