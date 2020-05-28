---
title: Använd python för att fråga en databas
description: Det här avsnittet visar hur du använder python för att skapa ett program som ansluter till en databas i Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2 
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 8286c304cc0ea557430fa3cf0240f1f33b92c838
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054415"
---
# <a name="quickstart-use-python-to-query-a-microsoft-azure-sql-database"></a>Snabb start: Använd python för att fråga en Microsoft Azure SQL-databas
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här snabb starten använder du python för att ansluta till en Azure SQL-databas och använda T-SQL-uttryck för att fråga efter data.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra den här snabbstarten:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  || SQL Database | SQL-hanterad instans | SQL Server på Azure VM |
  |:--- |:--- |:---|:---|
  | Skapa| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurera | [IP-brandväggsregel på servernivå](firewall-create-server-level-portal-quickstart.md)| [Anslutning från en virtuell dator](../managed-instance/connect-vm-instance-configure.md)|
  |||[Anslutning från en lokal plats](../managed-instance/point-to-site-p2s-configure.md) | [Anslut till SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Läsa in data|AdventureWorks som lästs in per snabbstart|[Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Återställa eller importera Adventure Works från [BACPAC](database-import.md) -filen från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Återställa eller importera Adventure Works från [BACPAC](database-import.md) -filen från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- [Python](https://python.org/downloads) 3 och relaterad program vara

  # <a name="macos"></a>[macOS](#tab/macos)

  Om du vill installera homebrew och python, ODBC-drivrutinen och SQLCMD och python-drivrutinen för SQL Server, använder du steg **1,2**, **1,3**och **2,1** i [skapa python-appar med SQL Server på MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Mer information finns i [Microsoft ODBC-drivrutin på MacOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Använd för att installera python och andra nödvändiga paket `sudo apt-get install python python-pip gcc g++ build-essential` .

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
> Om du vill skapa och konfigurera använder du [Azure Portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)eller [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)och sedan installerar du anslutningar [på plats](../managed-instance/point-to-site-p2s-configure.md) eller [virtuell dator](../managed-instance/connect-vm-instance-configure.md) .
>
> Om du vill läsa in data, se [restore with BACPAC](database-import.md) med [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -filen, eller se [återställa Wide World imports-databasen](../managed-instance/restore-sample-database-quickstart.md).

För att ytterligare utforska python och Azure SQL Database, se [Azure SQL Database-bibliotek för python](/python/api/overview/azure/sql), [pyodbc-lagringsplatsen](https://github.com/mkleehammer/pyodbc/wiki/)och ett [pyodbc-exempel](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL-databasen. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Gå till sidan **SQL-databaser** eller **SQL-hanterade instanser** .

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **Server namnet** för en Azure SQL Database eller det fullständigt kvalificerade Server namnet (eller IP-adressen) bredvid **värd** för en Azure SQL-hanterad instans eller SQL Server i en virtuell Azure-dator. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

> [!NOTE]
> Anslutnings information för SQL Server på en virtuell Azure-dator finns i [Anslut till SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-code-to-query-your-sql-database"></a>Skapa kod för att fråga din SQL-databas 

1. Skapa en ny fil med namnet *sqltest.py* i en textredigerare.  
   
1. Lägg till följande kod. Ersätt dina egna värden för \<server> , \<database> , \<username> och \<password> .
   
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

- [Utforma din första Azure SQL Database](design-first-database-tutorial.md)
- [Microsoft python-drivrutiner för SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python Developer Center](https://azure.microsoft.com/develop/python/?v=17.23h)

