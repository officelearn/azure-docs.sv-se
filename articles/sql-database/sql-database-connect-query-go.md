---
title: Använda Go för att köra frågor mot Azure SQL Database | Microsoft Docs
description: Använd Go för att skapa ett program som ansluter till en Azure SQL Database, och använd Transact-SQL-uttryck för att köra frågor mot och ändra data.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: v-daveng
ms.reviewer: MightyPen
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: 0014dc0edde0eafc153b40eec06c6bd6dc8446b5
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447873"
---
# <a name="quickstart-use-golang-to-query-an-azure-sql-database"></a>Snabbstart: Använda Golang för att fråga en Azure SQL-databas

I den här snabbstarten kommer du att använda programmeringsspråket [Golang](https://godoc.org/github.com/denisenkom/go-mssqldb) för att ansluta till en Azure SQL-databas. Därefter kommer du att köra Transact-SQL-uttryck för att fråga och redigera data. [Golang](https://golang.org/) är ett programmeringsspråk med öppen källkod som gör det enkelt att skapa enkel, pålitlig och effektiv programvara.  

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

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

- Golang och relaterad programvara för ditt operativsystem installerat:

  - **MacOS**: Installera Homebrew och Golang. Se [Steg 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
  - **Ubuntu**:  Installera Golang. Se [Steg 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
  - **Windows**: Installera Golang. Se [Steg 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Navigera till sidan **SQL-databaser** eller **SQL-hanterade instanser**.

3. På **översiktssidan** granskar du det fullständigt kvalificerade servernamnet bredvid **Servernamn** för en enkel databas eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och markerar ikonen **Kopiera**.

## <a name="create-golang-project-and-dependencies"></a>Skapa Golang-projekt och beroenden

1. Skapa från terminalen en ny projektmapp med namnet **SqlServerSample**. 

   ```bash
   mkdir SqlServerSample
   ```

2. Gå till **SqlServerSample** och installera SQL Server-drivrutinen för Go.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Skapa exempeldata

1. I en textredigerare skapar du en fil med namnet **CreateTestData.sql** i mappen **SqlServerSample**. Klistra in den här T-SQL-koden i filen. Det skapar ett schema, en tabell och infogar några rader.

   ```sql
   CREATE SCHEMA TestSchema;
   GO

   CREATE TABLE TestSchema.Employees (
     Id       INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
     Name     NVARCHAR(50),
     Location NVARCHAR(50)
   );
   GO

   INSERT INTO TestSchema.Employees (Name, Location) VALUES
     (N'Jared',  N'Australia'),
     (N'Nikita', N'India'),
     (N'Tom',    N'Germany');
   GO

   SELECT * FROM TestSchema.Employees;
   GO
   ```

2. Använd `sqlcmd` för att ansluta till databasen och köra det nya SQL-skriptet. Ersätt lämpliga värden för din server, databas, användarnamn och lösenord.

   ```bash
   sqlcmd -S <your_server>.database.windows.net -U <your_username> -P <your_password> -d <your_database> -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>Infoga kod för att fråga SQL-databas

1. Skapa en fil med namnet **sample.go** i mappen **SqlServerSample**.

2. Klistra in den här koden i filen. Lägg till värden för din server, databas, användarnamn och lösenord. Det här exemplet använder Golang [kontextmetoderna](https://golang.org/pkg/context/) för att se till att det finns en aktiv databasserveranslutning.

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
       "errors"
   )

   var db *sql.DB

   var server = "<your_server.database.windows.net>"
   var port = 1433
   var user = "<your_username>"
   var password = "<your_password>"
   var database = "<your_database>"

   func main() {
       // Build connection string
       connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s;",
           server, user, password, port, database)

       var err error

       // Create connection pool
       db, err = sql.Open("sqlserver", connString)
       if err != nil {
           log.Fatal("Error creating connection pool: ", err.Error())
       }
       ctx := context.Background()
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal(err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createID, err := CreateEmployee("Jake", "United States")
       if err != nil {
           log.Fatal("Error creating Employee: ", err.Error())
       }
       fmt.Printf("Inserted ID: %d successfully.\n", createID)

       // Read employees
       count, err := ReadEmployees()
       if err != nil {
           log.Fatal("Error reading Employees: ", err.Error())
       }
       fmt.Printf("Read %d row(s) successfully.\n", count)

       // Update from database
       updatedRows, err := UpdateEmployee("Jake", "Poland")
       if err != nil {
           log.Fatal("Error updating Employee: ", err.Error())
       }
       fmt.Printf("Updated %d row(s) successfully.\n", updatedRows)

       // Delete from database
       deletedRows, err := DeleteEmployee("Jake")
       if err != nil {
           log.Fatal("Error deleting Employee: ", err.Error())
       }
       fmt.Printf("Deleted %d row(s) successfully.\n", deletedRows)
   }

   // CreateEmployee inserts an employee record
   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           err = errors.New("CreateEmployee: db is null")
           return -1, err
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := "INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name, @Location); select convert(bigint, SCOPE_IDENTITY());"

       stmt, err := db.Prepare(tsql)
       if err != nil {
          return -1, err
       }
       defer stmt.Close()

       row := stmt.QueryRowContext(
           ctx,
           sql.Named("Name", name),
           sql.Named("Location", location))
       var newID int64
       err = row.Scan(&newID)
       if err != nil {
           return -1, err
       }

       return newID, nil
   }

   // ReadEmployees reads all employee records
   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           return -1, err
       }

       defer rows.Close()

       var count int

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // UpdateEmployee updates an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name = @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }

   // DeleteEmployee deletes an employee from the database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name = @Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>Kör koden

1. Kör följande kommando i kommandotolken.

   ```bash
   go run sample.go
   ```

2. Verifiera utdata.

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 row(s) successfully.
   Updated 1 row(s) successfully.
   Deleted 1 row(s) successfully.
   ```

## <a name="next-steps"></a>Nästa steg

- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)
- [Golang-drivrutin för Microsoft SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Rapportera problem eller ställ frågor](https://github.com/denisenkom/go-mssqldb/issues)

