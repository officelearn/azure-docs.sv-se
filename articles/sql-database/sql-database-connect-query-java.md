---
title: "Ansluta till Azure SQL Database med hjälp av Java | Microsoft Docs"
description: "Anger ett Java-kodexempel som du kan använda för att ansluta till och fråga en Azure SQL-databas."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 05/23/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 0df7bd56d3aac462a86e31b5512e48371918bbf2
ms.contentlocale: sv-se
ms.lasthandoff: 07/04/2017


---
# Azure SQL Database: Använd Java för att ansluta och skicka frågor till data
<a id="azure-sql-database-use-java-to-connect-and-query-data" class="xliff"></a>

Den här snabbstarten visar hur du använder [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) för att ansluta till en Azure SQL-databas. Använd sedan Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Microsoft-, Mac OS- och Ubuntu Linux-plattformar.

## Krav
<a id="prerequisites" class="xliff"></a>

Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter som utgångspunkt:

- [Skapa DB – Portal](sql-database-get-started-portal.md)
- [Skapa DB – CLI](sql-database-get-started-cli.md)
- [Skapa DB – PowerShell](sql-database-get-started-powershell.md)

## Installera Java-program
<a id="install-java-software" class="xliff"></a>

Stegen i det här avsnittet förutsätter att du är bekant med att utveckla med Java och att du är nybörjare när det gäller att arbeta med Azure SQL Database. Om du är nybörjare på utveckla med Java går du till [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Skapa en app med SQL Server) och väljer **Java** och sedan ditt operativsystem.

### **Mac OS**
<a id="mac-os" class="xliff"></a>
Öppna terminalen och navigera till den katalog där du vill skapa Java-projektet. Installera **brew** och **Maven** genom att ange följande kommandon: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

### **Linux (Ubuntu)**
<a id="linux-ubuntu" class="xliff"></a>
Öppna terminalen och navigera till den katalog där du vill skapa Java-projektet. Installera **Maven** genom att ange följande kommandon:

```bash
sudo apt-get install maven
```

### **Windows**
<a id="windows" class="xliff"></a>
Installera [Maven](https://maven.apache.org/download.cgi) med det officiella installationsprogrammet. Använd Maven för att hantera beroenden, skapa, testa och köra Java-projekt. 

## Hämta anslutningsinformation
<a id="get-connection-information" class="xliff"></a>

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet, databasnamnet och inloggningsinformationen i nästa procedurer.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. Granska serverns fullständiga namn på sidan **Översikt** för databasen, se bilden nedan. Om du hovrar över servernamnet visas alternativet **Kopiera genom att klicka**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Om du glömmer inloggningsinformationen för din server öppnar du serversidan i SQL Database. Där ser du administratörsnamnet för servern och kan återställa lösenordet vid behov.
5. Klicka på **Visa databasanslutningssträngar**.

6. Kontrollera den fullständiga **JDBC**-anslutningssträngen.

    ![JDBC-anslutningssträng](./media/sql-database-connect-query-jdbc/jdbc-connection-string.png)   

### **Skapa Maven-projekt**
<a id="create-maven-project" class="xliff"></a>
Skapa ett nytt Maven-projekt från terminalen. 
```bash
mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
```

Lägg till **Microsoft JDBC-drivrutinen för SQL Server** i beroenden i ***pom.xml***. 

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.2.0.jre8</version>
</dependency>
```

## Välj data
<a id="select-data" class="xliff"></a>

Använd följande kod för att söka efter de 20 främsta produkterna med funktionen [connection](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection) och transact-SQL-instruktionen [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Ersätt parametrar för värd, databas, användare och lösenord med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT. 

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
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## Infoga data
<a id="insert-data" class="xliff"></a>

Använd följande kod för att infoga en ny produkt i tabellen SalesLT.Product med [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) med Transact-SQL-instruktionen [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Ersätt parametrar för värd, databas, användare och lösenord med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, " 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                try (PreparedStatement prep = connection.prepareStatement(insertSql)) {
                        prep.setString(1, "BrandNewProduct");
                        prep.setInt(2, 200989);
                        prep.setString(3, "Blue");
                        prep.setDouble(4, 75);
                        prep.setDouble(5, 80);
                        prep.setTimestamp(6, sqlTimeStamp);

                        int count = prep.executeUpdate();
                        System.out.println("Inserted: " + count + " row(s)");
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
## Uppdatera data
<a id="update-data" class="xliff"></a>

Med följande kod uppdaterar du den nya produkt du tidigare lade till med [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) med Transact-SQL-instruktionen [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Ersätt parametrar för värd, databas, användare och lösenord med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Update data example:");
                System.out.println("=========================================");

                // Prepared statement to update data
                String updateSql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(updateSql)) {
                        prep.setString(1, "500");
                        prep.setString(2, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Updated: " + count + " row(s)")
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



## Ta bort data
<a id="delete-data" class="xliff"></a>

Med följande kod tar du bort den nya produkt du tidigare lade till med [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) med Transact-SQL-instruktionen [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Ersätt parametrar för värd, databas, användare och lösenord med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Delete data example:");
                System.out.println("=========================================");

                // Prepared statement to delete data
                String deleteSql = "DELETE SalesLT.Product WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(deleteSql)) {
                        prep.setString(1, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Deleted: " + count + " row(s)");
                }
        }       
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## Nästa steg
<a id="next-steps" class="xliff"></a>
- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft JDBC-drivrutin för SQL Server](https://github.com/microsoft/mssql-jdbc)
- [Rapportera problem/ställ frågor](https://github.com/microsoft/mssql-jdbc/issues)


