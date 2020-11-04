---
title: 'Snabb start: Anslut med C# – Azure Database for MySQL'
description: Den här snabbstarten innehåller ett kodexempel i C# (.NET) som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 10/16/2020
ms.openlocfilehash: 86362dc6d3e66f8b3d6888318fef0eb1dd12c3c3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337497"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql"></a>Snabb start: använda .NET (C#) för att ansluta och fråga efter data i Azure Database for MySQL

Den här snabbstarten visar hur du ansluter till en Azure Database for MySQL med hjälp av ett C#-program. Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Det här avsnittet förutsätter att du är van att utveckla i C# och att du saknar erfarenhet av Azure Database for MySQL.

## <a name="prerequisites"></a>Krav

I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa en Azure Database för MySQL med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

Du måste också:
- Installera [.net](https://www.microsoft.com/net/download). Följ stegen i den länkade artikeln för att installera .NET specifikt för din plattform (Windows, Ubuntu, Linux eller Mac OS). 
- Installera [Visual Studio](https://www.visualstudio.com/downloads/).

> [!IMPORTANT] 
> Se till att den IP-adress som du ansluter från har lagts till i serverns brand Väggs regler med hjälp av [Azure Portal](./howto-manage-firewall-using-portal.md) eller [Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="create-a-c-project"></a>Skapa ett C#-projekt
Kör följande i en kommandotolk:

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Skaffa den information som du behöver för att ansluta till Azure Database för MySQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser**. Sök sedan efter den server som du skapade (till exempel **mydemoserver** ).
3. Klicka på servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 :::image type="content" source="./media/connect-csharp/1_server-overview-name-login.png" alt-text="Azure Database för MySQL-servernamn":::

## <a name="connect-create-table-and-insert-data"></a>Ansluta, skapa tabell och infoga data
Använd följande kod för att ansluta och läsa in data med SQL-instruktionerna `CREATE TABLE` och `INSERT INTO`. Koden använder `MySqlConnection`-klassen med metoden [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) för att upprätta en anslutning till MySQL. Koden använder därefter metoden [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand), anger egenskapen CommandText och anropar metoden [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) för att köra databaskommandona. 

Ersätt parametrarna `Server`, `Database`, `UserID` och `Password` med de värden som du angav när du skapade servern och databasen. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="read-data"></a>Läsa data

Använd följande kod för att ansluta och läsa data med SQL-instruktionen `SELECT`. Koden använder `MySqlConnection`-klassen med metoden [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) för att upprätta en anslutning till MySQL. Koden använder sedan metoden [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) och metoden [ExecuteReaderAsync()](/dotnet/api/system.data.common.dbcommand.executereaderasync) för att köra databaskommandona. Därefter använder koden [ReadAsync()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) för att gå vidare till posterna i resultaten. Koden använder sedan GetInt32 och GetString för att parsa värdena i posten.

Ersätt parametrarna `Server`, `Database`, `UserID` och `Password` med de värden som du angav när du skapade servern och databasen. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att ansluta och läsa data med SQL-instruktionen `UPDATE`. Koden använder `MySqlConnection`-klassen med metoden [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) för att upprätta en anslutning till MySQL. Koden använder därefter metoden [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand), anger egenskapen CommandText och anropar metoden [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) för att köra databaskommandona. 

Ersätt parametrarna `Server`, `Database`, `UserID` och `Password` med de värden som du angav när du skapade servern och databasen. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och ta bort data med SQL-instruktionen `DELETE`. 

Koden använder `MySqlConnection`-klassen med metoden [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) för att upprätta en anslutning till MySQL. Koden använder därefter metoden [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand), anger egenskapen CommandText och anropar metoden [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) för att köra databaskommandona. 

Ersätt parametrarna `Server`, `Database`, `UserID` och `Password` med de värden som du angav när du skapade servern och databasen. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa alla resurser som används under den här snabb starten tar du bort resurs gruppen med hjälp av följande kommando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera MySQL-databasen till Azure Database för MySQL med säkerhetskopiering och återställning](concepts-migrate-dump-restore.md)