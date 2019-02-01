---
title: 'Självstudier: Elastisk fråga med Azure SQL Data Warehouse | Microsoft Docs'
description: Den här självstudien använder funktionen elastisk fråga för att fråga Azure SQL Data Warehouse från Azure SQL Database.
services: sql-data-warehouse
author: hirokib
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: b1ac2edd39ac2e5a765eaf6223ba01c9f9e5df91
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238352"
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Självstudier: Använda Elastic Query få åtkomst till data i Azure SQL Data Warehouse från Azure SQL Database

Den här självstudien använder funktionen elastisk fråga för att fråga Azure SQL Data Warehouse från Azure SQL Database. 

## <a name="prerequisites-for-the-tutorial"></a>Förutsättningar för självstudien

Innan du påbörjar självstudiekursen måste du ha följande krav:

1. Installerade SQL Server Management Studio (SSMS).
2. Skapa en Azure SQL server med en-databasen och informationslagret i den här servern.
3. Konfigurera brandväggsregler för att komma åt Azure SQL-Server.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Upprätta anslutning mellan SQL Data Warehouse och SQL Database-instanser 

1. Med hjälp av SSMS eller någon annan frågeklient öppnar du en ny fråga för databasen **master** på den logiska servern.

2. Skapa en inloggning och användare som representerar SQL-databasen till data warehouse-anslutning.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Med hjälp av SSMS eller någon annan frågeklient öppnar du en ny fråga för den **instans av SQL data warehouse** på den logiska servern.

4. Skapa en användare på informationslagerinstansen med inloggningen du skapade i steg 2

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Bevilja behörigheter för användaren från steg 4 som du vill SQL-databasen som ska köra. I det här exemplet är behörighet endast beviljas för väljer på ett visst schema som illustrerar hur vi kan begränsa frågor från SQL-databas till en viss domän. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Med hjälp av SSMS eller någon annan frågeklient öppnar du en ny fråga för den **SQL-databasinstans** på den logiska servern.

7. Skapa en huvudnyckel om du inte redan har en. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Skapa en databasbegränsade autentiseringsuppgifter med de autentiseringsuppgifter som du skapade i steg 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Skapa en extern datakälla som pekar till data warehouse-instans.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Nu kan du skapa externa tabeller som refererar till den externa datakällan. Frågor med hjälp av dessa tabeller skickas till data warehouse-instans som ska bearbetas och skickas tillbaka till databasinstansen.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Elastisk fråga från SQL-databas till SQL data warehouse

I följande steg ska vi skapa en tabell i våra data warehouse-instans med flera värden. Sedan visar vi hur du ställer in en extern tabell för att fråga data warehouse-instansen från databasinstansen.

1. Med hjälp av SSMS eller någon annan frågeklient öppnar du en ny fråga för den **SQL Data Warehouse** på den logiska servern.

2. Skicka följande fråga för att skapa en **OrdersInformation** tabell i din instans av informationslagret.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. Med hjälp av SSMS eller någon annan frågeklient öppnar du en ny fråga för den **SQL-databas** på den logiska servern.

4. Skicka följande fråga för att skapa en definition för extern tabell som pekar på den **OrdersInformation** tabellen i informationslagerinstansen.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Observera att du nu har en extern tabelldefinition i din **SQL-databasinstans**.

   ![elastisk fråga externa tabelldefinitionen](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Skicka följande fråga som frågar data warehouse-instans. Du bör få fem värden som du har infogats i steg 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Observera att den här frågan tar lång tid att returnera trots få värden. När du använder elastisk fråga med data warehouse, bör en omkostnader för frågebearbetning och rörelse över nätverket. Använd elastisk fråga fjärrkörning när beräkningskraft inte svarstid, är prioritet.

Grattis, du har ställt in grunder elastisk fråga. 

## <a name="next-steps"></a>Nästa steg
Rekommendationer finns i [bästa praxis för att använda elastisk fråga med Azure SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md).
