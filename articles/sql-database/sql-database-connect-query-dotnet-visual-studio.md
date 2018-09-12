---
title: Använd Visual Studio och .NET för att fråga Azure SQL Database | Microsoft Docs
description: Det här avsnittet visar hur du använder Visual Studio för att skapa ett program som ansluter till en Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck.
services: sql-database
ms.workload: azure-vs
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: devcenter, vs-azure
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: da2bd556567882d2620e9bc8b1f58923010eeaa4
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050025"
---
# <a name="use-net-c-with-visual-studio-to-connect-and-query-an-azure-sql-database"></a>Använd .NET (C#) med Visual Studio för att ansluta och skicka frågor till en Azure SQL-databas

Den här snabbstarten visar hur du använder [.NET Framework](https://www.microsoft.com/net/) till att skapa ett C#-program med Visual Studio för anslutning till en Azure SQL-databas och använder Transact-SQL-instruktioner till att köra frågor mot data.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan genomföra den här snabbstarten:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- En [brandväggsregel på servernivå](sql-database-get-started-portal-firewall.md) för den offentliga IP-adressen till datorn som du använder för den här snabbstarten.

- En installation av [Visual Studio Community 2017, Visual Studio Professional 2017 eller Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="for-adonet"></a>För ADO.NET

1. Fortsätt genom att klicka på **Visa databasanslutningssträngar**.

2. Granska den fullständiga **ADO.NET**-anslutningssträngen.

    ![ADO.NET-anslutningssträng](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> Du måste ha en brandväggsregel för den offentliga IP-adressen för datorn som du utför den här självstudien med. Om du använder en annan dator eller har en annan offentlig IP-adress så skapar du en [brandväggsregel på servernivå med hjälp av Azure Portal](sql-database-get-started-portal-firewall.md). 
>
  
## <a name="create-a-new-visual-studio-project"></a>Skapa ett nytt Visual Studio-projekt

1. I Visual Studio väljer du **Arkiv**, **Nytt** och **Projekt**. 
2. I dialogrutan **Nytt projekt** expanderar du **Visual C#**.
3. Välj **Konsolprogram** och ange *sqltest* som projektnamn.
4. Klicka på **OK** för att skapa och öppna det nya projektet i Visual Studio
4. Högerklicka på **sqltest** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**. 
5. På fliken **Bläddra** söker du efter ```System.Data.SqlClient``` och markerar det när du hittar det.
6. På sidan **System.Data.SqlClient** klickar du på **Installera**.
7. När installationen har slutförts granskar du ändringarna och klickar på **OK** för att stänga fönstret **Förhandsgranska**. 
8. Om ett fönster för **Godkännande av licens** visas klickar du på **Jag accepterar**.

## <a name="insert-code-to-query-sql-database"></a>Infoga kod för att fråga SQL-databas
1. Växla till (eller öppna vid behov) **Program.cs**

2. Ersätt innehållet i **Program.cs** med följande kod och lägg till lämpliga värden för din server, databas, användare och lösenord.

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

1. Tryck på **F5** för att köra programmet.
2. Kontrollera att de 20 översta raderna returneras och stäng sedan programfönstret.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [ansluter och frågar en Azure SQL-databas med .NET Core](sql-database-connect-query-dotnet-core.md) på Windows/Linux/macOS.  
- Lär dig mer om att [Komma igång med .NET Core för Windows/Linux/macOS med hjälp av kommandoraden](/dotnet/core/tutorials/using-with-xplat-cli).
- Lär dig hur du [Utformar din första Azure SQL-databas med hjälp av SSMS](sql-database-design-first-database.md) eller [Utformar din första Azure SQL-databas med hjälp av .NET](sql-database-design-first-database-csharp.md).
- Mer information om .NET finns i [.NET-dokumentationen](https://docs.microsoft.com/dotnet/).
- [Exempel på logik för omprövning: Ansluta elastiskt till SQL med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

