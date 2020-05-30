---
title: Använd Java för att fråga en databas
description: Visar hur du använder Java för att skapa ett program som ansluter till en databas i Azure SQL Database och frågar den med hjälp av T-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019, sqldbrb=2 
ms.openlocfilehash: 4e5ee26b1cfa686eb501e0c6b6ba7905ea687094
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84189477"
---
# <a name="quickstart-use-java-to-query-a-database-in-azure-sql-database"></a>Snabb start: Använd Java för att fråga en databas i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här snabb starten använder du Java för att ansluta till en databas i Azure SQL Database och använder T-SQL-uttryck för att fråga efter data.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra den här snabbstarten:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  || SQL Database | SQL-hanterad instans | SQL Server på virtuella Azure-datorer |
  |:--- |:--- |:---|:---|
  | Skapa| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurera | [IP-brandväggsregel på servernivå](firewall-create-server-level-portal-quickstart.md)| [Anslutning från en virtuell dator](../managed-instance/connect-vm-instance-configure.md)|
  |||[Anslutning från lokal plats](../managed-instance/point-to-site-p2s-configure.md) | [Ansluta till en SQL Server-instans](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Läsa in data|AdventureWorks som lästs in per snabbstart|[Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Återställa eller importera Adventure Works från en [BACPAC](database-import.md) -fil från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Återställa eller importera Adventure Works från en [BACPAC](database-import.md) -fil från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)-relaterad program vara

  # <a name="macos"></a>[macOS](#tab/macos)

  Installera homebrew och Java och installera sedan maven med steg **1,2** och **1,3** i [skapa Java-appar med SQL Server på MacOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installera Java, installera Java Development Kit och installera sedan maven med steg **1,2**, **1,3**och **1,4** i [skapa Java-appar med SQL Server på Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Installera Java och installera sedan maven med steg **1,2** och **1,3** i [skapa Java-appar med hjälp av SQL Server i Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> Skripten i den här artikeln är skrivna för att använda **Adventure Works** -databasen.

> [!NOTE]
> Alternativt kan du välja att använda en hanterad Azure SQL-instans.
>
> Om du vill skapa och konfigurera använder du [Azure Portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)eller [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)och konfigurerar sedan [lokal](../managed-instance/point-to-site-p2s-configure.md) eller [virtuell dator](../managed-instance/connect-vm-instance-configure.md) anslutning.
>
> Om du vill läsa in data, se [restore with BACPAC](database-import.md) med [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -filen, eller se [återställa Wide World imports-databasen](../managed-instance/restore-sample-database-quickstart.md).

## <a name="get-server-connection-information"></a>Hämta information om Server anslutning

Hämta anslutnings informationen du behöver för att ansluta till databasen i Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **SQL-databaser** eller öppna sidan **SQL-hanterade instanser** .

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **Server namnet** för en databas i Azure SQL Database eller det fullständigt kvalificerade Server namnet (eller IP-adressen) bredvid **värd** för en Azure SQL-hanterad instans eller SQL Server på Azure VM. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

> [!NOTE]
> Anslutnings information för SQL Server på den virtuella Azure-datorn finns i [Anslut till SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

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

## <a name="add-code-to-query-the-database"></a>Lägg till kod för att fråga databasen

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
   > I kod exemplet används exempel databasen **AdventureWorksLT** i Azure SQL Database.

## <a name="run-the-code"></a>Kör koden

1. Kör appen i kommandotolken.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Kontrol lera att de 20 översta raderna returneras och Stäng app-fönstret.

## <a name="next-steps"></a>Nästa steg

- [Utforma din första databas i Azure SQL Database](design-first-database-tutorial.md)  
- [Microsoft JDBC-drivrutin för SQL Server](https://github.com/microsoft/mssql-jdbc)  
- [Rapportera problem/ställ frågor](https://github.com/microsoft/mssql-jdbc/issues)  
