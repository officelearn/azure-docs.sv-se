---
title: Ansluta med Python - Azure Database för MySQL
description: I den här snabbstarten finns flera kodexempel i Python som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
ms.devlang: python
ms.topic: quickstart
ms.date: 01/09/2020
ms.openlocfilehash: c9ea155f3cc71dd961a3780e3b188a6d062606bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067903"
---
# <a name="quickstart-use-python-to-connect-and-query-data-with-azure-database-for-mysql"></a>Snabbstart: Använd Python för att ansluta och fråga data med Azure Database för MySQL

I den här snabbstarten ansluter du till en Azure-databas för MySQL med hjälp av Python. Du kan sedan använda SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Mac-, Ubuntu Linux- och Windows-plattformar. 

Det här avsnittet förutsätter att du är bekant med att utveckla med Python, men du har inte tid med att arbeta med Azure Database for MySQL.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En Azure-databas för MySQL-server. [Skapa en Azure-databas för MySQL-server med Azure-portalen](quickstart-create-mysql-server-database-using-azure-portal.md) eller [Skapa en Azure-databas för MySQL-server med Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="install-python-and-the-mysql-connector"></a>Installera Python och MySQL Connector

Installera Python och MySQL-kontakten för Python på datorn med hjälp av följande steg: 

> [!NOTE]
> Den här snabbstarten använder en rå SQL-frågemetod för att ansluta till MySQL. Om du använder ett webbramverk använder du den rekommenderade anslutningen för ramverket, till exempel [mysqlclient](https://pypi.org/project/mysqlclient/) för Django.

1. Ladda ner och installera [Python 3.7 eller högre](https://www.python.org/downloads/) för ditt operativsystem. Se till att lägga `PATH`till Python i din , eftersom MySQL-kopplingen kräver det.
   
1. Öppna en kommandotolk eller `bash` ett skal `python -V` och kontrollera Python-versionen genom att köra med växeln versaler V.
   
1. Paketinstallationsprogrammet `pip` ingår i de senaste versionerna av Python. Uppdatera `pip` till den senaste versionen genom att köra `pip install -U pip`. 
   
   Om `pip` den inte är installerad kan `get-pip.py`du hämta och installera den med . Mer information finns i [Installation](https://pip.pypa.io/en/stable/installing/). 
   
1. Används `pip` för att installera MySQL-anslutningen för Python och dess beroenden:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   Du kan också installera Python-anslutningen för MySQL från [mysql.com](https://dev.mysql.com/downloads/connector/python/). Mer information om MySQL Connector för Python finns i [Utvecklarhandboken för MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Hämta den anslutningsinformation du behöver för att ansluta till Azure Database for MySQL från Azure-portalen. Du behöver servernamn, databasnamn och inloggningsuppgifter.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
   
1. Sök efter och välj den Azure Database for MySQL-server som du skapade i sökfältet, till exempel **mydemoserver**.
   
   ![Azure Database for MySQL-servernamn](./media/connect-python/1_server-overview-name-login.png)
   
1. Anteckna **servernamnet** och **serveradministratörens inloggningsnamn**på **serverns** översiktssida . Om du glömmer ditt lösenord kan du också återställa lösenordet från den här sidan.
   
   ![Azure Database for MySQL-servernamn](./media/connect-python/azure-database-for-mysql-server-overview-name-login.png)

## <a name="run-the-python-examples"></a>Kör Python-exemplen

För varje kodexempel i den här artikeln:

1. Skapa en ny fil i en textredigerare.
1. Lägg till kodexemplet i filen. I koden ersätter `<mydemoserver>` `<myadmin>`du `<mypassword>`platshållarna , , och `<mydatabase>` med värdena för MySQL-servern och -databasen.
1. Spara filen i en projektmapp med ett *py-tillägg,* till exempel *C:\pythonmysql\createtable.py* eller */home/username/pythonmysql/createtable.py*.
1. Om du vill köra koden `bash` öppnar du en kommandotolk eller `cd pythonmysql`ett skal och ändrar katalogen till projektmappen, till exempel . Skriv `python` kommandot följt av filnamnet, `python createtable.py`till exempel , och tryck på Retur. 
   
   > [!NOTE]
   > Om *python.exe* inte hittas i Windows kan du behöva lägga till Python-sökvägen i sökvägsmiljövariabeln eller ange den fullständiga sökvägen till *python.exe*, till exempel `C:\python27\python.exe createtable.py`.

## <a name="create-a-table-and-insert-data"></a>Skapa en tabell och infoga data

Använd följande kod för att ansluta till servern och databasen, skapa en tabell och läsa in data med hjälp av ett **INSERT** SQL-uttryck. 

Koden importerar mysql.connector-biblioteket och använder funktionen [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) för att ansluta till Azure Database för MySQL med [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) i config-samlingen. Koden använder en markör på anslutningen och metoden [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) kör SQL-frågan mot MySQL-databasen. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Läsa data

Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion. 

Koden importerar mysql.connector-biblioteket och använder funktionen [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) för att ansluta till Azure Database för MySQL med [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) i config-samlingen. Koden använder en markör på anslutningen och metoden [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) kör SQL-frågan mot MySQL-databasen. 

Koden läser dataraderna med metoden [fetchall(),](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) behåller resultatuppsättningen i en `for` samlingsrad och använder en iterator för att loopa över raderna.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Uppdatera data

Använd följande kod för att ansluta och uppdatera data med en **UPDATE**-SQL-instruktion. 

Koden importerar mysql.connector-biblioteket och använder funktionen [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) för att ansluta till Azure Database för MySQL med [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) i config-samlingen. Koden använder en markör på anslutningen och metoden [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) kör SQL-frågan mot MySQL-databasen. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Ta bort data

Använd följande kod för att ansluta och ta bort data med en **DELETE**-SQL-instruktion. 

Koden importerar mysql.connector-biblioteket och använder funktionen [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) för att ansluta till Azure Database för MySQL med [argumenten](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) i config-samlingen. Koden använder en markör på anslutningen och metoden [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) kör SQL-frågan mot MySQL-databasen. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./concepts-migrate-import-export.md)
