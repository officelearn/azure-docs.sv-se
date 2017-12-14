---
title: "Elastisk frågan självstudier med Azure SQL Data Warehouse | Microsoft Docs"
description: "Lär dig mer om elastiska frågan med Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67g
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 11/03/2017
ms.author: elbutter
ms.openlocfilehash: 20bbdbbde7edc4351563685761785874870a3c82
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="configure-elastic-query-with-sql-data-warehouse"></a>Konfigurera elastisk fråga med SQL Data Warehouse

I kursen får du lära dig hur du använder elastisk frågan för att skicka en fråga från SQL-databas till SQL Data Warehouse. Elastisk frågan är en funktion som finns mellan Azure SQL-produkter. Läs mer om elastiska Query som ett koncept, [ **använda elastisk fråga med SQL Data Warehouse**][How to use Elastic Query with SQL Data Warehouse].

## <a name="prerequisites-for-the-tutorial"></a>Förutsättningar för självstudiekursen

Innan du påbörjar självstudiekursen måste du ha följande krav:

1. Installera SQL Server Management Studio (SSMS).
2. Skapa en Azure SQL-server med en databas och informationslagret i den här servern.
3. Konfigurera brandväggsregler för åtkomst till Azure SQL Server.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Konfigurera anslutning mellan instanser av SQL Data Warehouse och SQL-databas 

1. Med hjälp av SSMS eller en annan fråga klienten, öppna en ny fråga för databasen **master** på din logiska server.

2. Skapa en inloggning och användaren som representerar SQL-databasen till data warehouse-anslutning.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Med hjälp av SSMS eller en annan fråga klienten, öppna en ny fråga för den **SQL data warehouse-instans** på din logiska server.

4. Skapa en användare på informationslagerinstansen med inloggning som du skapade i steg 2

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Bevilja behörighet till användaren från steg 4 som du vill SQL-databasen som ska köras. I det här exemplet är behörighet bara beviljas för väljer på ett visst schema som illustrerar hur vi kan begränsa frågor från SQL-databasen till en viss domän. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Med hjälp av SSMS eller en annan fråga klienten, öppna en ny fråga för den **SQL-databasinstans** på din logiska server.

7. Skapa en huvudnyckel om du inte redan har en. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Skapa en databasbegränsade autentiseringsuppgift med de autentiseringsuppgifter som du skapade i steg 2.

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

10. Du kan nu skapa externa tabeller som refererar till den externa datakällan. Frågor med dessa tabeller skickas till data warehouse-instans som ska bearbetas och skickas tillbaka till databasinstansen.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Elastisk frågan från SQL-databas till SQL data warehouse

I följande steg ska vi skapa en tabell i våra data warehouse-instans med flera värden. Sedan visar vi hur du ställer in en extern tabell för att fråga informationslagerinstansen från databasinstansen.

1. Med hjälp av SSMS eller en annan fråga klienten, öppna en ny fråga för den **SQL Data Warehouse** på din logiska server.

2. Skicka följande fråga för att skapa en **OrdersInformation** tabellen i din data warehouse-instans.

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

3. Med hjälp av SSMS eller en annan fråga klienten, öppna en ny fråga för den **SQL-databas** på din logiska server.

4. Skicka följande fråga om du vill skapa en externa tabelldefinitionen som pekar på den **OrdersInformation** tabellen i data warehouse-instans.

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

5. Observera att du nu har en externa tabelldefinitionen din **SQL-databasinstans**.

   ![elastisk frågedefinitionen för extern tabell](./media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Skicka följande fråga som frågar data warehouse-instans. Du bör få fem värden som har infogats i steg 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Observera att den här frågan tar lång tid att returnera trots några värden. När du använder elastisk frågan med datalagret, bör en omkostnaderna frågebearbetningen och flytt via kabel. Använda elastisk frågan fjärrkörning när datorkraft inte svarstiden är prioriteten.

Grattis, du har ställt in de första grunderna för elastiska fråga. 




<!--Image references-->

<!--Article references-->

[How to use Elastic Query with SQL Data Warehouse]: ./how-to-use-elastic-query-with-sql-data-warehouse.md

<!--MSDN references-->

<!--Other Web references-->
