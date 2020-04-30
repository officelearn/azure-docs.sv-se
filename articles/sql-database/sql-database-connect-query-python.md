---
title: Använd python för att fråga en databas
description: Det här avsnittet visar hur du använder Python för att skapa ett program som ansluter till en Azure SQL-databas och frågar den med hjälp av Transact-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76768578"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Snabbstart: Använda Python för att köra frågor mot en Azure SQL-databas

I den här snabb starten använder du python för att ansluta till en Azure SQL-databas och använda T-SQL-uttryck för att fråga efter data.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En [Azure SQL-databas](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 och relaterad program vara

  # <a name="macos"></a>[macOS](#tab/macos)

  Om du vill installera homebrew och python, ODBC-drivrutinen och SQLCMD och python-drivrutinen för SQL Server, använder du steg **1,2**, **1,3**och **2,1** i [skapa python-appar med SQL Server på MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Mer information finns i [Microsoft ODBC-drivrutin på MacOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Använd `sudo apt-get install python python-pip gcc g++ build-essential`för att installera python och andra nödvändiga paket.

  Om du vill installera ODBC-drivrutinen, SQLCMD och python-drivrutinen för SQL Server, se [Konfigurera en miljö för Pyodbc python-utveckling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Mer information finns i [Microsoft ODBC-drivrutin på Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Om du vill installera python, ODBC-drivrutinen och SQLCMD och python-drivrutinen för SQL Server, se [Konfigurera en miljö för Pyodbc python-utveckling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Mer information finns i [Microsoft ODBC-drivrutin](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Skripten i den här artikeln är skrivna för att använda **Adventure Works** -databasen.

> [!NOTE]
> Alternativt kan du välja att använda en hanterad Azure SQL-instans.
>
> Om du vill skapa och konfigurera använder du [Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)eller [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)och sedan installerar du anslutningar [på plats](sql-database-managed-instance-configure-p2s.md) eller [virtuell dator](sql-database-managed-instance-configure-vm.md) .
>
> Om du vill läsa in data, se [restore with BACPAC](sql-database-import.md) med [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -filen, eller se [återställa Wide World imports-databasen](sql-database-managed-instance-get-started-restore.md).

För att ytterligare utforska python och Azure SQL Database, se [Azure SQL Database-bibliotek för python](/python/api/overview/azure/sql), [pyodbc-lagringsplatsen](https://github.com/mkleehammer/pyodbc/wiki/)och ett [pyodbc-exempel](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL-databasen. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Gå till sidan **SQL-databaser** eller **SQL-hanterade instanser** .

3. På **översiktssidan** granskar du det fullständigt kvalificerade servernamnet bredvid **Servernamn** för en enkel databas eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

## <a name="create-code-to-query-your-sql-database"></a>Skapa kod för att fråga din SQL-databas 

1. Skapa en ny fil med namnet *sqltest.py* i en textredigerare.  
   
1. Lägg till följande kod. Ersätt med dina egna värden för \<server>, \<database>, \<username> och \<password>.
   
   >[!IMPORTANT]
   >Koden i det här exemplet använder AdventureWorksLT-exempeldata, som du kan välja som källa när du skapar din databas. Om din databas har andra data använder du tabeller från din egen databas i SELECT-frågan. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Kör koden

1. Kör följande kommando i en kommandotolk:

   ```cmd
   python sqltest.py
   ```

1. Kontrollera att de översta 20 raderna för kategori/produkt returneras och stäng sedan kommandofönstret.

## <a name="next-steps"></a>Nästa steg

- [Utforma din första Azure SQL-databas](sql-database-design-first-database.md)
- [Microsoft python-drivrutiner för SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python Developer Center](https://azure.microsoft.com/develop/python/?v=17.23h)

