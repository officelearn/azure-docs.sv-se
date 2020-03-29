---
title: Implementera en geodistribuerad lösning
description: Lär dig att konfigurera din Azure SQL-databas och program för redundans till en replikerad databas och testa redundans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 58d5bd4a7f3087e11056354f7534c3c9dbebca3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067290"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Självstudiekurs: Implementera en geodistribuerad databas

Konfigurera en Azure SQL-databas och ett program för redundans till en fjärrregion och testa en redundansplan. Lär dig att:

> [!div class="checklist"]
> - Skapa en [redundansgrupp](sql-database-auto-failover-group.md)
> - Köra ett Java-program för att fråga en Azure SQL-databas
> - Redundanstest

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

Kontrollera att du har installerat följande objekt för att slutföra självstudien:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- En enda databas i Azure SQL Database. Om du vill skapa en
  - [Portal](sql-database-single-database-get-started.md)
  - [CLI](sql-database-cli-samples.md)
  - [Powershell](sql-database-powershell-samples.md)

  > [!NOTE]
  > Självstudien använder *exempeldatabasen AdventureWorksLT.*

- Java och Maven, se [Skapa en app med SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), markera **Java** och välj din miljö och följ sedan stegen.

> [!IMPORTANT]
> Var noga med att ställa in brandväggsregler för att använda den offentliga IP-adressen för den dator där du utför stegen i den här självstudien. Brandväggsregler på databasnivå replikeras automatiskt till den sekundära servern.
>
> Information finns i [Skapa en brandväggsregel på databasnivå](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) eller för att fastställa vilken IP-adress som används för brandväggsregeln på servernivå för datorn finns [i Skapa en brandvägg på servernivå](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Skapa en redundansgrupp

Med Azure PowerShell skapar du [redundansgrupper](sql-database-auto-failover-group.md) mellan en befintlig Azure SQL-server och en ny Azure SQL-server i en annan region. Lägg sedan till exempeldatabasen i redundansgruppen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Om du vill skapa en redundansgrupp kör du följande skript:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> Kör `az login` för att logga in på Azure.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

Inställningar för georeplikering kan också ändras i Azure-portalen genom att välja databasen och sedan **Inställningar** > **Geo-Replication**.

![Inställningar för georeplikering](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Kör exempelprojektet

1. Skapa ett Maven-projekt med följande kommando i konsolen:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Skriv **Y** och tryck på **Retur**.

1. Ändra kataloger till det nya projektet.

   ```bash
   cd SqlDbSample
   ```

1. Öppna *filen pom.xml* i projektmappen med din favoritredigerare.

1. Lägg till Microsoft JDBC-drivrutinen för `dependency` SQL Server-beroende genom att lägga till följande avsnitt. Beroendet måste klistras in `dependencies` i det större avsnittet.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Ange Java-versionen genom `properties` att `dependencies` lägga till avsnittet efter avsnittet:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Stöd manifestfiler genom `build` att `properties` lägga till avsnittet efter avsnittet:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Spara och stäng *filen pom.xml.*

1. Öppna filen *App.java* som finns i .. \SqlDbSample\src\main\java\com\sqldbsamples och ersätt innehållet med följande kod:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Spara och stäng *filen App.java.*

1. Kör följande kommando i kommandokonsolen:

   ```bash
   mvn package
   ```

1. Starta programmet som körs i ca 1 timme tills det stoppas manuellt, så att du kan köra redundanstestet.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Redundanstest

Kör följande skript för att simulera en redundans och observera programresultaten. Lägg märke till hur vissa infogningar och markeringar misslyckas under databasmigreringen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Du kan kontrollera rollen för haveriberedskapsservern under testet med följande kommando:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Så här testar du en redundans:

1. Starta en manuell redundansväxling för redundansgruppen:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Återgår till redundansgruppen till den primära servern:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan kontrollera rollen för haveriberedskapsservern under testet med följande kommando:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Så här testar du en redundans:

1. Starta en manuell redundansväxling för redundansgruppen:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Återgår till redundansgruppen till den primära servern:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du en Azure SQL-databas och ett program för redundans till en fjärrregion och testade en redundansplan. Du har lärt dig att:

> [!div class="checklist"]
> - Skapa en redundansgrupp för geo-replikering
> - Köra ett Java-program för att fråga en Azure SQL-databas
> - Redundanstest

Gå vidare till nästa handledning om hur du migrerar med DMS.

> [!div class="nextstepaction"]
> [Migrera SQL Server till Azure SQL-databashanterad instans med DMS](../dms/tutorial-sql-server-to-managed-instance.md)
