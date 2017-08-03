---
title: "Använd Visual Studio och .NET för att fråga Azure SQL Database | Microsoft Docs"
description: "Det här avsnittet visar hur du använder Visual Studio för att skapa ett program som ansluter till en Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/05/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 105dab17823a7e7f6957a604833f4ecad35c14bd
ms.contentlocale: sv-se
ms.lasthandoff: 07/28/2017

---
# <a name="use-net-c-with-visual-studio-to-connect-and-query-an-azure-sql-database"></a>Använd .NET (C#) med Visual Studio för att ansluta och skicka frågor till en Azure SQL-databas

Den här snabbstartskursen visar hur du använder [.NET-ramverket](https://www.microsoft.com/net/) för att skapa ett C#-program med Visual Studio för att ansluta till en Azure SQL-databas och använda Transact-SQL-uttryck för att fråga data.

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande för att slutföra den här snabbstartskursen:

- En Azure SQL-databas. Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter: 

   - [Skapa DB – Portal](sql-database-get-started-portal.md)
   - [Skapa DB – CLI](sql-database-get-started-cli.md)
   - [Skapa DB – PowerShell](sql-database-get-started-powershell.md)

- En [brandväggsregel på servernivå](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) för den offentliga IP-adressen för datorn som du använder för den här snabbstartskursen.
- En installation av [Visual Studio Community 2017, Visual Studio Professional 2017 eller Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet, databasnamnet och inloggningsinformationen i nästa procedurer.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. Granska serverns fullständiga namn på sidan **Översikt** för databasen, se bilden nedan. Om du hovrar över servernamnet visas alternativet **Kopiera genom att klicka**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Om du glömmer inloggningsinformationen för din Azure SQL Database-server öppnar du serversidan i SQL Database. Där ser du administratörsnamnet för servern. Du kan återställa lösenordet om det behövs.

5. Klicka på **Visa databasanslutningssträngar**.

6. Granska den fullständiga **ADO.NET**-anslutningssträngen.

    ![ADO.NET-anslutningssträng](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> Du måste ha en brandväggsregel för den offentliga IP-adressen för datorn som du utför den här självstudien med. Om du använder en annan dator eller har en annan offentlig IP-adress så skapar du en [brandväggsregel på servernivå med hjälp av Azure Portal](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 
>
  
## <a name="create-a-new-visual-studio-project"></a>Skapa ett nytt Visual Studio-projekt

1. I Visual Studio väljer du **Arkiv**, **Nytt** och **Projekt**. 
2. I dialogrutan **Nytt projekt** expanderar du **Visual C#**.
3. Välj **Konsolprogram** och ange *sqltest* som projektnamn.
4. Klicka på **OK** för att skapa och öppna det nya projektet i Visual Studio
4. Högerklicka på **sqltest** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**. 
5. På **Bläddra** söker du efter ```System.Data.SqlClient``` och markerar det när du hittar det.
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

