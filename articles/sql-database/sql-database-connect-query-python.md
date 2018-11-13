---
title: Fråga Azure SQL Database med Python | Microsoft Docs
description: Det här avsnittet visar hur du använder Python för att skapa ett program som ansluter till en Azure SQL Database och frågar den med hjälp av Transact-SQL-uttryck.
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
ms.date: 11/01/2018
ms.openlocfilehash: 2ab5b7a5b17daef00cb62f69a7d2a798c18456bb
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912959"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Snabbstart: Använda Python för att köra frågor mot en Azure SQL-databas

 Den här snabbstarten visar hur du använder [Python](https://python.org) för att ansluta till en Azure SQL-databas och hur du använder Transact-SQL-uttryck för att köra frågor mot data. För ytterligare information om sdk ska du titta på vår [referensdokumentation](https://docs.microsoft.com/python/api/overview/azure/sql), ett pyodbc[exempel](https://github.com/mkleehammer/pyodbc/wiki/Getting-started) och [pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) GitHub-lagringsplatsen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan genomföra den här snabbstarten:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- En [brandväggsregel på servernivå](sql-database-get-started-portal-firewall.md) för den offentliga IP-adressen till datorn som du använder för den här snabbstarten.

- Du har installerat Python och relaterad programvara för ditt operativsystem:

    - **MacOS**: Först installerar du Homebrew och Python, sedan ODBC-drivrutinen och SQLCMD och sedan installerar du Python-drivrutinen för SQL Server. Se [steg 1.2, 1.3 och 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).
    - **Ubuntu**: Först installerar du Python och andra paket som krävs, sedan installerar du Python-drivrutinen för SQL Server. Se [steg 1.2, 1.3 och 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/).
    - **Windows**: Först installerar du den senaste versionen av Python (miljövariabeln är konfigurerad för dig), sedan ODBC-drivrutinen och SQLCMD och sedan installerar du Python-drivrutinen för SQL Server. Se [Steg 1.2, 1.3 och 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/windows/). 

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Infoga kod för att fråga SQL-databas 

1. Skapa en ny fil, **sqltest.py**, i valfri textredigerare.  

2. Ersätt innehållet med följande kod och lägg till lämpliga värden för din server, databas, användare och lösenord.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>Kör koden

1. Kör följande kommandon i kommandotolken:

   ```Python
   python sqltest.py
   ```

2. Kontrollera att de 20 översta raderna returneras och stäng sedan programfönstret.

## <a name="next-steps"></a>Nästa steg

- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft Python-drivrutiner för SQLServer](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python Developer Center](https://azure.microsoft.com/develop/python/?v=17.23h)

