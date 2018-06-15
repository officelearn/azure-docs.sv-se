---
title: Implementera en geo-distribuerad Azure SQL Database-lösning | Microsoft Docs
description: Läs om hur du konfigurerar din Azure SQL-databas och ditt program för redundansväxling till en replikerad databas och testar redundans.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,business continuity
ms.topic: tutorial
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: fbd239c3c8c11b1907a6d28eb95d2c0ad26cfe61
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31416627"
---
# <a name="implement-a-geo-distributed-database"></a>Implementera en geo-distribuerad databas

I den här självstudiekursen konfigurerar du en Azure SQL-databas och ett program för redundansväxling till en fjärregion och testar sedan din redundansplan. Lär dig att: 

> [!div class="checklist"]
> * Skapa databasanvändare och ge dem behörigheter
> * Skapa en brandväggsregel på databasnivå
> * Skapa en [redundansgrupp för geo-replikering](sql-database-geo-replication-overview.md)
> * Skapa och kompilera ett Java-program för att fråga en Azure SQL-databas
> * Utföra ett programåterställningstest

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.


## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Du måste ha installerat senaste versionen av [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs). 
- Du måste ha installerat en Azure SQL-databas. I den här självstudiekursen används exempeldatabasen AdventureWorksLT med namnet **mySampleDatabase** från någon av dessa snabbstarter:

   - [Skapa DB – Portal](sql-database-get-started-portal.md)
   - [Skapa DB – CLI](sql-database-get-started-cli.md)
   - [Skapa DB – PowerShell](sql-database-get-started-powershell.md)

- Identifiera en metod för att köra SQL-skript mot din databas. Du kan använda något av följande frågeverktyg:
   - Frågeredigeraren i [Azure Portal](https://portal.azure.com). Mer information om hur du använder frågeredigeraren i Azure Portal finns i [Connect and query using Query Editor](sql-database-get-started-portal.md#query-the-sql-database) (Anslut och fråga med frågeredigeraren).
   - Den senaste versionen av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), som är en integrerad miljö för att hantera all SQL-infrastruktur från SQL Server till SQL Database för Microsoft Windows.
   - Den senaste versionen av [Visual Studio Code](https://code.visualstudio.com/docs), som är en grafisk kodredigerare för Linux, macOS och Windows som stöder tillägg, inklusive [mssql-tillägget](https://aka.ms/mssql-marketplace) för frågor till Microsoft SQL Server, Azure SQL Database och SQL Data Warehouse. Mer information om hur du använder det här verktyget med Azure SQL Database finns i [Connect and query with VS Code](sql-database-connect-query-vscode.md) (Anslut och fråga med VS Code). 

## <a name="create-database-users-and-grant-permissions"></a>Skapa databasanvändare och ge dem behörigheter

Anslut till din databas och skapa användarkonton med hjälp av något av följande frågeverktyg:

- Frågeredigeraren i Azure Portal
- SQL Server Management Studio
- Visual Studio-koden

Dessa användarkonton replikeras automatiskt till den sekundära servern (och hålls synkroniserade). Om du vill använda SQL Server Management Studio eller Visual Studio Code kan du behöva konfigurera en brandväggsregel om du ansluter från en klient på en IP-adress som du ännu inte har konfigurerat en brandvägg för. Detaljerade anvisningar finns i [Skapa en brandväggsregel på servernivå](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- I ett frågefönster skriver du följande fråga för att skapa två användarkonton i databasen. Det här skriptet ger **db_owner**-behörighet till **app_admin**-kontot och ger **SELECT**- och **UPDATE**-behörighet till **app_user**-kontot. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Skapa en brandväggsregel på databasnivå

Skapa en [brandväggsregel på databasnivå](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) för din SQL-databas. Den här brandväggsregeln på databasnivå replikeras automatiskt till den sekundära servern som du skapar i den här kursen. För enkelhetens skull använder du den allmänna IP-adressen för datorn som du använder i den här självstudien. Läs [Skapa en brandväggsregel på servernivå](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) för att ta reda på vilken IP-adress som används för brandväggsregeln på servernivå för din aktuella dator.  

- I det öppna frågefönstret ersätter du den tidigare frågan med följande fråga, vilket ersätter IP-adresserna med lämpliga IP-adresser för din miljö.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>Skapa en redundansgrupp för aktiv geo-replikering 

Med Azure PowerShell skapar du en [redundansgrupp för aktiv geo-replikering](sql-database-geo-replication-overview.md) mellan din befintliga Azure SQL-server och den nya tomma Azure SQL-servern i en Azure-region. Lägg sedan till din exempeldatabas i redundansgruppen.

> [!IMPORTANT]
> Dessa cmdletar kräver Azure PowerShell 4.0. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Fyll i variabler för ditt PowerShell-skript med hjälp av värdena för din befintliga server och exempeldatabasen, och ange ett globalt unikt värde för namnet på redundansgruppen.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. Skapa en tom säkerhetskopieringsserver i din redundansregion.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. Skapa en redundansgrupp mellan de två servrarna.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. Lägg till din databas i redundansgruppen.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Installera Java-program

Stegen i det här avsnittet förutsätter att du är bekant med att utveckla med Java och att du är nybörjare när det gäller att arbeta med Azure SQL Database. 

### <a name="mac-os"></a>**Mac OS**
Öppna terminalen och navigera till den katalog där du vill skapa Java-projektet. Installera **brew** och **Maven** genom att ange följande kommandon: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Om du vill ha detaljerad information om hur du installerar och konfigurerar Java- och Maven-miljön går du till [Skapa en app med SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), väljer **Java**, väljer **MacOS** och följer de detaljerade anvisningarna om hur du konfigurerar Java och Maven i steg 1.2 och 1.3.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Öppna terminalen och navigera till den katalog där du vill skapa Java-projektet. Installera **Maven** genom att ange följande kommandon:

```bash
sudo apt-get install maven
```

Om du vill ha detaljerad information om hur du installerar och konfigurerar Java- och Maven-miljön går du till [Skapa en app med SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), väljer **Java**, väljer **Ubuntu** och följer de detaljerade anvisningarna om hur du konfigurerar Java och Maven i steg 1.2, 1.3 och 1.4.

### <a name="windows"></a>**Windows**
Installera [Maven](https://maven.apache.org/download.cgi) med det officiella installationsprogrammet. Använd Maven för att hantera beroenden, skapa, testa och köra Java-projekt. Om du vill ha detaljerad information om hur du installerar och konfigurerar Java- och Maven-miljön går du till [Skapa en app med SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), väljer **Java**, väljer Windows och följer de detaljerade anvisningarna om hur du konfigurerar Java och Maven i steg 1.2 och 1.3.

## <a name="create-sqldbsample-project"></a>Skapa SqlDbSample-projekt

1. Skapa ett Maven-projekt i kommandokonsolen (till exempel Bash). 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. Skriv **Y** och tryck på **Retur**.
3. Ändra kataloger till det nyskapade projektet.

   ```bash
   cd SqlDbSamples
   ```

4. Öppna pom.xml-filen i din projektmapp med hjälp av en redigerare. 

5. Lägg till Microsoft JDBC-drivrutinen för SQL Server-beroendet i Maven-projektet genom att öppna valfri textredigerare och kopiera och klistra in följande rader i pom.xml-filen. Skriv inte över de befintliga förifyllda värdena i filen. JDBC-beroendet måste klistras in i det större ”dependencies”-avsnittet (beroenden).   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Ange den version av Java som projektet ska kompileras mot genom att lägga till följande ”properties”-avsnitt (egenskaper) i pom.xml-filen efter ”dependencies”-avsnittet (beroenden). 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. Lägg till följande ”build”-avsnitt (version) i pom.xml-filen efter ”properties”-avsnittet (egenskaper) för att stödja manifestfiler i jar-filer.       

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
8. Spara och stäng filen pom.xml.
9. Öppna App.java-filen (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) och ersätt innehållet med följande innehåll. Ersätt namnet på redundansgruppen med namnet på din redundansgrupp. Om du har ändrat värdena för databasnamn, användare eller lösenord ändrar du även dessa värden.

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

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

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
      // Query the data that was previously inserted into the primary database from the geo replicated database
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
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
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
6. Spara och stäng filen App.java.

## <a name="compile-and-run-the-sqldbsample-project"></a>Kompilera och köra projektet SqlDbSample

1. Kör följande kommando i kommandokonsolen.

   ```bash
   mvn package
   ```
2. När det är klart kör du följande kommando för att köra programmet (det körs i ungefär 1 timme om du inte stoppar det manuellt):

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Utföra ett programåterställningstest

1. Anropa manuell redundans för redundansgruppen. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Se programresultaten under redundansväxlingen. Vissa infogningar misslyckas medan DNS-cachen uppdateras.     

3. Ta reda på vilken roll din haveriberedskapsserver utför.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Återställ efter fel.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Se programresultaten under redundansväxlingen. Vissa infogningar misslyckas medan DNS-cachen uppdateras.     

6. Ta reda på vilken roll din haveriberedskapsserver utför.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du lärt dig att konfigurera en Azure SQL-databas och ett program för redundansväxling till en fjärregion och sedan testa din redundansplan.  Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa databasanvändare och ge dem behörigheter
> * Skapa en brandväggsregel på databasnivå
> * Skapa en redundansgrupp för geo-replikering
> * Skapa och kompilera ett Java-program för att fråga en Azure SQL-databas
> * Utföra ett programåterställningstest

Gå vidare till nästa självstudie där du får lära dig att skapa anpassade avbildningar för hanterade enheter.

> [!div class="nextstepaction"]
>[Skapa en hanterad instans](sql-database-managed-instance-create-tutorial-portal.md)

