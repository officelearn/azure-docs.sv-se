---
title: "Ansluta till Azure Database för MySQL från C# | Microsoft Docs"
description: "Den här snabbstarten innehåller ett kodexempel i C# (.NET) som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL."
services: MySQL
author: seanli1988
ms.author: seal
manager: janders
editor: jasonwhowell
ms.service: MySQL
ms.custom: mvc
ms.devlang: csharp
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: f87c3c302ec25f33af4334c3753dfae0084e4393
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-net-c-to-connect-and-query-data"></a>Azure Database för MySQL: Använda .NET (C#) för att ansluta och fråga efter data
Den här snabbstarten visar hur du ansluter till en Azure Database for MySQL med hjälp av ett C#-program. Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Det här avsnittet förutsätter att du är van att utveckla i C# och att du saknar erfarenhet av Azure Database for MySQL.

## <a name="prerequisites"></a>Krav
I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa en Azure Database för MySQL med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

Du måste också:
- Installera [.NET](https://www.microsoft.com/net/download). Följ stegen i den länkade artikeln för att installera .NET specifikt för din plattform (Windows, Ubuntu, Linux eller Mac OS). 
- [Installera Visual Studio](https://www.visualstudio.com/downloads/).
- Installera [ODBC Driver för MySQL](https://dev.mysql.com/downloads/connector/odbc/).

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Skaffa den information som du behöver för att ansluta till Azure Database för MySQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser** och söker efter den server som du nyss skapade (till exempel **myserver4demo**).
3. Klicka på servernamnet.
4. Välj sidan **Egenskaper** för servern och notera **Servernamn** och **Inloggningsnamn för serveradministratören**.
 ![Azure Database för MySQL-servernamn](./media/connect-csharp/1_server-properties-name-login.png)
5. Om du glömmer inloggningsinformationen för servern öppnar du sidan **Översikt** för att se inloggningsnamnet för serveradministratören. Om det behövs kan du återställa lösenordet.

## <a name="connect-create-table-and-insert-data"></a>Ansluta, skapa tabell och infoga data
Använd följande kod för att ansluta och läsa in data med hjälp av SQL-instruktionerna **CREATE TABLE** och **INSERT INTO**. Koden använder klassen QDBC med metoden [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) för att upprätta en anslutning till MySQL. Sedan använder koden metoden [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), anger egenskapen CommandText och anropar metoden [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) för att köra databaskommandona. 

Ersätt parametrarna Host, DBName, User och Password med de värden som du angav när du skapade servern och databasen. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data;
using System.Data.Odbc;

namespace driver
{
    class MySQLCreate
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "DROP TABLE IF EXISTS inventory;";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished dropping table (if existed)");

            command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished creating table");

            command.CommandText =
                String.Format(
                    @"INSERT INTO inventory (name, quantity) VALUES ({0}, {1});
                    INSERT INTO inventory (name, quantity) VALUES ({2}, {3});
                    INSERT INTO inventory (name, quantity) VALUES ({4}, {5});",
                    "\'banana\'", 150,
                    "\'orange\'", 154,
                    "\'apple\'", 100
                    );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows inserted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }

    }
}

```

## <a name="read-data"></a>Läsa data

Använd följande kod för att ansluta och läsa data med SQL-instruktionen **SELECT**. Koden använder klassen QDBC med metoden [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) för att upprätta en anslutning till MySQL. Sedan används metoden [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx) och metoden [ExecuteReader()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executereader(v=vs.110).aspx) för att köra databaskommandona. Metoden [Read()](https://msdn.microsoft.com/library/system.data.odbc.odbcdatareader.read(v=vs.110).aspx) används sedan för att gå vidare till posterna i resultaten. Koden använder sedan GetInt32 och GetString för att parsa värdena i posten.

Ersätt parametrarna Host, DBName, User och Password med de värden som du angav när du skapade servern och databasen. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data;
using System.Data.Odbc;


namespace driver
{
    class MySQLRead
    {

        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "SELECT * FROM inventory;";

            var reader = command.ExecuteReader();
            while (reader.Read())
            {
                Console.WriteLine(
                    string.Format(
                        "Reading from table=({0}, {1}, {2})",
                        reader.GetInt32(0).ToString(),
                        reader.GetString(1),
                        reader.GetInt32(2).ToString()
                        )
                    );
            }

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}


```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att ansluta och läsa data med SQL-instruktionen **UPDATE**. Koden använder klassen QDBC med metoden [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) för att upprätta en anslutning till MySQL. Sedan använder koden metoden [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), anger egenskapen CommandText och anropar metoden [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) för att köra databaskommandona.

Ersätt parametrarna Host, DBName, User och Password med de värden som du angav när du skapade servern och databasen. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Odbc;

namespace driver
{
    class MySQLUpdate
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
            String.Format("UPDATE inventory SET quantity = {0} WHERE name = {1};",
                200,
                "\'banana\'"
                );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows updated={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}



```


## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och läsa data med SQL-instruktionen **DELETE**. 

Koden använder klassen QDBC med metoden [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) för att upprätta en anslutning till MySQL. Sedan använder koden metoden [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), anger egenskapen CommandText och anropar metoden [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) för att köra databaskommandona.

Ersätt parametrarna Host, DBName, User och Password med de värden som du angav när du skapade servern och databasen. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Odbc;

namespace driver
{
    class MySQLDelete
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
                String.Format("DELETE FROM inventory WHERE name = {0};",
                    "\'orange\'");
            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows deleted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}

```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera MySQL-databasen till Azure Database för MySQL med säkerhetskopiering och återställning](concepts-migrate-dump-restore.md)
