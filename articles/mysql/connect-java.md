---
title: 'Snabb start: Anslut med Java-Azure Database for MySQL'
description: Den här snabb starten innehåller ett Java-kod exempel som du kan använda för att ansluta och fråga efter data från en Azure Database for MySQL databas.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, devx-track-java
ms.topic: quickstart
ms.devlang: java
ms.date: 5/26/2020
ms.openlocfilehash: b6f928aba1c3abda57a8ed329c0ad4e7cdb5e881
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185901"
---
# <a name="quickstart-use-java-to-connect-to-and-query-data-in-azure-database-for-mysql"></a>Snabb start: Använd Java för att ansluta till och fråga efter data i Azure Database for MySQL

I den här snabb starten ansluter du till en Azure Database for MySQL med hjälp av ett Java-program och JDBC-drivrutinen MariaDB Connector/J. Sedan använder du SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Mac, Ubuntu Linux och Windows-plattformar. 

Det här avsnittet förutsätter att du är bekant med att utveckla med Java, men du är inte nybörjare på att arbeta med Azure Database for MySQL.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En Azure Database for MySQL-server. [Skapa en Azure Database for MySQL-server med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) eller [skapa en Azure Database for MySQL-server med Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).
- Azure Database for MySQL anslutnings säkerhet konfigureras med brand väggen öppnad och SSL-anslutnings inställningar som kon figurer ATS för programmet.

> [!IMPORTANT] 
> Se till att den IP-adress som du ansluter från har lagts till i serverns brand Väggs regler med hjälp av [Azure Portal](./howto-manage-firewall-using-portal.md) eller [Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="obtain-the-mariadb-connector"></a>Hämta MariaDB-anslutningen

Hämta [MariaDB Connector/J-](https://mariadb.com/kb/en/library/mariadb-connector-j/) anslutningen med någon av följande metoder:
   - Använd Maven-paketet [MariaDB-Java-client](https://search.maven.org/search?q=a:mariadb-java-client) för att inkludera [MariaDB-Java-client-beroendet](https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client) i Pom-filen för projektet.
   - Hämta JDBC-drivrutinen [MariaDB Connector/J](https://downloads.mariadb.org/connector-java/) och ta med JDBC jar-filen (till exempel MariaDB-Java-client-2.4.3. jar) i sökvägen till program klassen. Se din miljös dokumentation för information om klass Sök vägar, till exempel [Apache Tomcat](https://tomcat.apache.org/tomcat-7.0-doc/class-loader-howto.html) eller [Java se](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/classpath.html)

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Skaffa den information som du behöver för att ansluta till Azure Database för MySQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal väljer du **alla resurser**och söker sedan efter den server som du har skapat (till exempel **mydemoserver**).
3. Välj servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 ![Azure Database för MySQL-servernamn](./media/connect-java/azure-database-mysql-server-name.png)

## <a name="connect-create-table-and-insert-data"></a>Ansluta, skapa tabell och infoga data

Använd följande kod för att ansluta och läsa in data med hjälp av en funktion med en **INSERT**-SQL-instruktion. Metoden [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) används för att ansluta till MySQL. Metoderna [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) och execute() används för att släppa och skapa tabellen. Objektet prepareStatement används för att skapa infogningskommandon och setString() och setInt() används för att binda parametervärdena. Metoden executeUpdate() infogar värdena genom att kommandot körs för varje uppsättning parametrar. 

Ersätt parametrarna host, database, user och password med de värden som du angav när du skapade din server och databas.

```java
import java.sql.*;
import java.util.Properties;

public class CreateTableInsertRows {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Drop previous table of same name if one exists.
                Statement statement = connection.createStatement();
                statement.execute("DROP TABLE IF EXISTS inventory;");
                System.out.println("Finished dropping table (if existed).");
    
                // Create table.
                statement.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
                System.out.println("Created table.");
                
                // Insert some data into table.
                int nRowsInserted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("INSERT INTO inventory (name, quantity) VALUES (?, ?);");
                preparedStatement.setString(1, "banana");
                preparedStatement.setInt(2, 150);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "orange");
                preparedStatement.setInt(2, 154);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "apple");
                preparedStatement.setInt(2, 100);
                nRowsInserted += preparedStatement.executeUpdate();
                System.out.println(String.format("Inserted %d row(s) of data.", nRowsInserted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
    
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}

```

## <a name="read-data"></a>Läsa data

Använd följande kod för att läsa in data med en **SELECT**-SQL-instruktion. Metoden [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) används för att ansluta till MySQL. Metoderna [createStatement ()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) och executeQuery () används för att ansluta och köra SELECT-instruktionen. Resultaten bearbetas med ett ResultSet-objekt. 

Ersätt parametrarna host, database, user och password med de värden som du angav när du skapade din server och databas.

```java
import java.sql.*;
import java.util.Properties;

public class ReadTable {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
    
                Statement statement = connection.createStatement();
                ResultSet results = statement.executeQuery("SELECT * from inventory;");
                while (results.next())
                {
                    String outputString = 
                        String.format(
                            "Data row = (%s, %s, %s)",
                            results.getString(1),
                            results.getString(2),
                            results.getString(3));
                    System.out.println(outputString);
                }
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database."); 
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="update-data"></a>Uppdatera data

Använd följande kod för att ändra data med en **UPDATE**-SQL-instruktion. Metoden [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) används för att ansluta till MySQL. Metoderna [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) och executeUpdate() används för att förbereda och köra update-instruktionen. 

Ersätt parametrarna host, database, user och password med de värden som du angav när du skapade din server och databas.

```java
import java.sql.*;
import java.util.Properties;

public class UpdateTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Modify some data in table.
                int nRowsUpdated = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?;");
                preparedStatement.setInt(1, 200);
                preparedStatement.setString(2, "banana");
                nRowsUpdated += preparedStatement.executeUpdate();
                System.out.println(String.format("Updated %d row(s) of data.", nRowsUpdated));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="delete-data"></a>Ta bort data

Använd följande kod för att ta bort data med en **DELETE**-SQL-instruktion. Metoden [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) används för att ansluta till MySQL.  Metoderna [prepareStatement ()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) och executeUpdate () används för att förbereda och köra Delete-instruktionen. 

Ersätt parametrarna host, database, user och password med de värden som du angav när du skapade din server och databas.

```java
import java.sql.*;
import java.util.Properties;

public class DeleteTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Delete some data from table.
                int nRowsDeleted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("DELETE FROM inventory WHERE name = ?;");
                preparedStatement.setString(1, "orange");
                nRowsDeleted += preparedStatement.executeUpdate();
                System.out.println(String.format("Deleted %d row(s) of data.", nRowsDeleted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera MySQL-databasen till Azure Database för MySQL med säkerhetskopiering och återställning](concepts-migrate-dump-restore.md)
