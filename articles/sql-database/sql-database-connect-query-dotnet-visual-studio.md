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
ms.date: 03/25/2019
ms.openlocfilehash: 7c444235ed7aa641fe218b4d1667e661ff05d77e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409752"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Snabbstart: Använda .NET och C# i Visual Studio för att ansluta till och köra frågor mot en Azure SQL-databas

Den här snabbstarten beskriver hur du använder [.NET Framework](https://www.microsoft.com/net/) och C#-kod i Visual Studio för att köra frågor mot en Azure SQL-databas med Transact-SQL-instruktioner.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att slutföra den här snabbstarten:

- En Azure SQL-databas. Du kan använda någon av dessa snabbstarter för att skapa och därefter konfigurera en databas i Azure SQL Database:

  || Enskild databas | Hanterad instans |
  |:--- |:--- |:---|
  | Skapa| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurera | [IP-brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md)| [Anslutning från en virtuell dator](sql-database-managed-instance-configure-vm.md)|
  |||[Anslutning från en lokal plats](sql-database-managed-instance-configure-p2s.md)
  |Läsa in data|AdventureWorks som lästs in per snabbstart|[Återställa Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Återställa eller importera Adventure Works från [BACPAC](sql-database-import.md) fil från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripten i den här artikeln skrivs för att använda Adventure Works-databasen. Med en hanterad instans måste du antingen importera Adventure Works-databasen till en instansdatabas eller ändra skripten i den här artikeln om du vill använda Wide World Importers-databasen.

- [Visual Studio 2017](https://www.visualstudio.com/downloads/) Community, Professional- eller Enterprise-utgåvan.

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL-databasen. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Navigera till sidan **SQL-databaser** eller **SQL-hanterade instanser**.

3. På **översiktssidan** granskar du det fullständigt kvalificerade servernamnet bredvid **Servernamn** för en enkel databas eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och markerar ikonen **Kopiera**. 

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
- Lär dig hur du [utformar din första Azure SQL-databas med hjälp av SSMS](sql-database-design-first-database.md) eller [utformar din första Azure SQL-databas med hjälp av .NET](sql-database-design-first-database-csharp.md).
- Mer information om .NET finns i [.NET-dokumentationen](https://docs.microsoft.com/dotnet/).
- Exempel på omprövningslogik: [Ansluta elastiskt till SQL med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

