---
title: Använda Visual Studio med .NET och C# för att köra frågor mot Azure SQL Database | Microsoft Docs
description: Använd Visual Studio för att skapa en C#-app som ansluter till en Azure SQL-databas och kör frågor mot den med Transact-SQL-instruktioner.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/11/2018
ms.openlocfilehash: 93249b7d274ce9d7928dfa46eb339da68c92b785
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163304"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Snabbstart: Använda .NET och C# i Visual Studio för att ansluta till och köra frågor mot en Azure SQL-databas

Den här snabbstarten beskriver hur du använder [.NET Framework](https://www.microsoft.com/net/) och C#-kod i Visual Studio för att köra frågor mot en Azure SQL-databas med Transact-SQL-instruktioner.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att slutföra den här snabbstarten:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) Community, Professional- eller Enterprise-utgåvan.

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-the-sql-database"></a>Skapa kod för att fråga SQL-databasen

1. Välj **Arkiv** > **Nytt** > **Projekt** i Visual Studio. 
   
1. Välj **Visual C#** i dialogrutan **Nytt projekt** och välj sedan **Konsolprogram (.NET Framework)**.
   
1. Ange *sqltest* som projektnamn och välj sedan **OK**. Det nya projektet skapas. 
   
1. Välj **Projekt** > **Hantera NuGet-paket**. 
   
1. Välj fliken **Bläddra**  i **NuGet-pakethanteraren** och leta upp och välj **System.Data.SqlClient**.
   
1. Välj **Installera** på sidan för **System.Data.SqlClient**. 
   - Välj **OK** för att fortsätta med installationen om du uppmanas att göra det. 
   - Om ett fönster för **godkännande av licensen** visas väljer du **Jag accepterar**.
   
1. När installationen har slutförts kan du stänga **NuGet-pakethanteraren**. 
   
1. Ersätt innehållet i **Program.cs** med följande kod i kodredigeraren. Ersätt värdena för `<server>`, `<username>`, `<password>` och `<database>`.
   
   >[!IMPORTANT]
   >Koden i det här exemplet använder AdventureWorksLT-exempeldata, som du kan välja som källa när du skapar din databas. Om din databas har andra data använder du tabeller från din egen databas i SELECT-frågan. 
   
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
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
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

1. Kör appen genom att välja **Felsök** > **Starta felsökning**, genom att välja **Starta** i verktygsfältet eller genom att trycka på **F5**.
1. Kontrollera att de 20 främsta raderna för kategori/produkt från databasen returneras och stäng sedan appfönstret.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [ansluter och frågar en Azure SQL-databas med .NET Core](sql-database-connect-query-dotnet-core.md) i Windows/Linux/macOS.  
- Lär dig mer om att [Komma igång med .NET Core för Windows/Linux/macOS med hjälp av kommandoraden](/dotnet/core/tutorials/using-with-xplat-cli).
- Lär dig hur du [Utformar din första Azure SQL-databas med hjälp av SSMS](sql-database-design-first-database.md) eller [Utformar din första Azure SQL-databas med hjälp av .NET](sql-database-design-first-database-csharp.md).
- Mer information om .NET finns i [.NET-dokumentationen](https://docs.microsoft.com/dotnet/).
- Exempel på omprövningslogik: [Ansluta elastiskt till SQL med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

