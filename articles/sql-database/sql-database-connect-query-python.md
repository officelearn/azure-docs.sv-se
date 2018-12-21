---
title: Fråga Azure SQL Database med Python | Microsoft Docs
description: Det här avsnittet visar hur du använder Python för att skapa ett program som ansluter till en Azure SQL-databas och frågar den med hjälp av Transact-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: b9c33da4f002504a55802e4253d648ff87847d92
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271835"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Snabbstart: Fråga Azure SQL Database med Python

 Den här snabbstarten visar hur du använder [Python](https://python.org) för att ansluta till en Azure SQL-databas och hur du använder Transact-SQL-uttryck för att köra frågor mot data. Ytterligare SDK-information finns i vår [referensdokumentation](https://docs.microsoft.com/python/api/overview/azure/sql), [pyodbc-lagringsplatsen på GitHub](https://github.com/mkleehammer/pyodbc/wiki/) och i ett [pyodbc-exempel](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan genomföra den här snabbstarten:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- En [brandväggsregel på servernivå](sql-database-get-started-portal-firewall.md) för den offentliga IP-adressen till datorn som du använder för den här snabbstarten.
  
- Python och relaterad programvara för ditt operativsystem:
  
  - **MacOS**: Först installerar du Homebrew och Python, sedan ODBC-drivrutinen och SQLCMD och sedan installerar du Python-drivrutinen för SQL Server. Se steg 1.2, 1.3 och 2.1 i [Create Python apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/) (Skapa Python-appar med SQL Server på macOS). Mer information finns i [Install the Microsoft ODBC Driver on Linux and macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server) (Installera Microsoft ODBC-drivrutinen i Linux och macOS).
    
  - **Ubuntu**: Installera Python och andra paket som krävs med `sudo apt-get install python python-pip gcc g++ build-essential`. Ladda ned och installera ODBC-drivrutinen, SQLCMD och Python-drivrutinen för SQL Server. Anvisningar finns i [Configure a development environment for pyodbc Python development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux) (Konfigurera en utvecklingsmiljö för utveckling med Python och pyodbc).
    
  - **Windows**: Installera Python, ODBC-drivrutinen och SQLCMD, samt Python-drivrutinen för SQL Server. Anvisningar finns i [Configure a development environment for pyodbc Python development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows) (Konfigurera en utvecklingsmiljö för utveckling med Python och pyodbc).

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
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

- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft Python-drivrutiner för SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python Developer Center](https://azure.microsoft.com/develop/python/?v=17.23h)

