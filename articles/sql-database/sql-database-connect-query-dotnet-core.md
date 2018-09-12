---
title: Köra frågor mot Azure SQL Database med hjälp av .NET Core | Microsoft Docs
description: Det här avsnittet beskriver hur du använder .NET Core för att skapa ett program som ansluter till en Azure SQL-databas och hur du kör frågor mot databasen med hjälp av Transact-SQL-uttryck.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: eadab6972a18af65cc510ad76b6f80a1de35ebf1
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44051956"
---
# <a name="use-net-core-c-to-query-an-azure-sql-database"></a>Köra frågor mot Azure SQL Database med hjälp av .NET Core (C#)

Den här snabbstarten beskriver hur du använder [.NET Core](https://www.microsoft.com/net/) i Windows/Linux/Mac OS för att skapa ett C#-program som ansluter till en Azure SQL-databas och använder Transact-SQL-instruktioner till att köra frågor mot data.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan genomföra den här snabbstarten:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- En [brandväggsregel på servernivå](sql-database-get-started-portal-firewall.md) för den offentliga IP-adressen till datorn som du använder för den här snabbstarten.

- Du har installerat [.NET Core för ditt operativsystem](https://www.microsoft.com/net/core). 

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="for-adonet"></a>För ADO.NET

1. Fortsätt genom att klicka på **Visa databasanslutningssträngar**.

2. Granska den fullständiga **ADO.NET**-anslutningssträngen.

    ![ADO.NET-anslutningssträng](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> Du måste ha en brandväggsregel för den offentliga IP-adressen för datorn som du utför den här självstudien med. Om du använder en annan dator eller har en annan offentlig IP-adress så skapar du en [brandväggsregel på servernivå med hjälp av Azure Portal](sql-database-get-started-portal-firewall.md). 
>
  
## <a name="create-a-new-net-project"></a>Skapa ett nytt .NET-projekt

1. Öppna en kommandotolk och skapa en mapp med namnet *sqltest*. Navigera till den mapp som du har skapat och kör följande kommando:

    ```
    dotnet new console
    ```

2. Öppna ***sqltest.csproj*** med valfri textredigerare och lägg till System.Data.SqlClient som ett beroende med hjälp av följande kod:

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.4.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Infoga kod för att fråga SQL Database

1. Öppna **Program.cs** i din utvecklingsmiljö eller textredigerare

2. Ersätt innehållet med följande kod och lägg till lämpliga värden för server, databas, användare och lösenord.

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.ReadLine();
        }
    }
}
```

## <a name="run-the-code"></a>Kör koden

1. Kör följande kommandon i kommandotolken:

   ```csharp
   dotnet restore
   dotnet run
   ```

2. Kontrollera att de 20 översta raderna returneras och stäng sedan programfönstret.


## <a name="next-steps"></a>Nästa steg

- [Komma igång med .NET Core för Windows/Linux/macOS med hjälp av kommandoraden](/dotnet/core/tutorials/using-with-xplat-cli).
- Lär dig hur du [ansluter till och frågar en Azure SQL Database med .NET Framework och Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Lär dig hur du [utformar din första Azure SQL Database med hjälp av SSMS](sql-database-design-first-database.md) eller [utformar din första Azure SQL Database med hjälp av .NET](sql-database-design-first-database-csharp.md).
- Mer information om .NET finns i [.NET-dokumentationen](https://docs.microsoft.com/dotnet/).
