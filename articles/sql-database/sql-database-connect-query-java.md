---
title: Fråga Azure SQL Database med Java | Microsoft Docs
description: Visar hur du använder Java för att skapa ett program som ansluter till en Azure SQL-databas och köra frågor mot den med hjälp av SQL-instruktioner.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 2d9ce34d52d08b4dd38caaadfab48b7a69870e9a
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447940"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Snabbstart: Fråga Azure SQL Database med Java

Den här artikeln visar hur du använder [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) för att ansluta till en Azure SQL-databas. Du kan sedan använda T-SQL-instruktioner för att köra frågor mot data.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra det här exemplet behöver du följande:

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

- Java-relaterad programvara installerad för ditt operativsystem:

  - **MacOS**, installera Homebrew och Java och sedan Maven. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  - **Ubuntu**, installera Java Development Kit och sedan Maven. Se [Steg 1.2, 1.3 och 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  - **Windows**, installera Java och sedan Maven. Se [steg 1.2 och 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Navigera till sidan **SQL-databaser** eller **SQL-hanterade instanser**.

3. På **översiktssidan** granskar du det fullständigt kvalificerade servernamnet bredvid **Servernamn** för en enkel databas eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och markerar ikonen **Kopiera**. 

## <a name="create-the-project"></a>Skapa projektet

1. Från kommandotolken skapar du ett nytt Maven-projekt som du namnger *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Bytt mapp till *sqltest* och öppna *pom.xml* med valfri textredigerare. Lägg till **Microsoft JDBC-drivrutinen för SQL Server** i projektets beroenden med hjälp av följande kod.

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. Lägg även till följande egenskaper i projektet i filen *pom.xml*. Om du inte har ett avsnitt för egenskaper kan du lägga till efter beroendena.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Spara och stäng *pom.xml*.

## <a name="add-code-to-query-database"></a>Lägga till kod i frågedatabas

1. Du bör redan ha en fil med namnet *App.java* i ditt Maven-projekt, som finns i:

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Öppna filen och ersätt innehållet med följande kod. Lägg sedan till lämpliga värden för servern, databas, användare och lösenord.

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > Kodexemplet använder exempeldatabasen **AdventureWorksLT** för SQL Azure.

## <a name="run-the-code"></a>Kör koden

1. Kör appen i kommandotolken.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Kontrollera att de 20 översta raderna returneras och stäng appfönstret.

## <a name="next-steps"></a>Nästa steg

- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)  

- [Microsoft JDBC-drivrutin för SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Rapportera problem/ställ frågor](https://github.com/microsoft/mssql-jdbc/issues)  
