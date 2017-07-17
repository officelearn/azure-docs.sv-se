---
title: "Ansluta till Azure SQL Database med hjälp av Python | Microsoft Docs"
description: "Anger ett Python-kodexempel som du kan använda för att ansluta till och fråga en Azure SQL-databas."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 99195b43a1577f978562864bac5fa12cdeb95d63
ms.contentlocale: sv-se
ms.lasthandoff: 06/17/2017

---
# Azure SQL Database: Använd Python för att ansluta och skicka frågor till data
<a id="azure-sql-database-use-python-to-connect-and-query-data" class="xliff"></a>

 Den här snabbstarten visar hur du använder [Python](https://python.org) för att ansluta till en Azure SQL-databas. Använd sedan Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data i databasen från Microsoft-, Mac OS- och Ubuntu Linux-plattformar.

## Krav
<a id="prerequisites" class="xliff"></a>

Den här snabbstarten använder resurser som har skapats i någon av dessa snabbstarter som utgångspunkt:

- [Skapa DB – Portal](sql-database-get-started-portal.md)
- [Skapa DB – CLI](sql-database-get-started-cli.md)
- [Skapa DB – PowerShell](sql-database-get-started-powershell.md)

## Installera Python- och databaskommunikationsbibliotek
<a id="install-the-python-and-database-communication-libraries" class="xliff"></a>

Stegen i det här avsnittet förutsätter att du är bekant med att utveckla med Python och att du är nybörjare när det gäller att arbeta med Azure SQL Database. Om du är nybörjare på utveckla med Python går du till [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Skapa en app med SQL Server) och väljer **Python** och sedan ditt operativsystem.

### **Mac OS**
<a id="mac-os" class="xliff"></a>
Öppna terminalen och navigera till den katalog där du vill skapa Python-skriptet. Ange följande kommandon för att installera **brew**, **Microsoft ODBC-drivrutin för Mac** och **pyodbc**. pyodbc använder Microsoft ODBC Driver på Linux för att ansluta till SQL-databaser.

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### **Linux (Ubuntu)**
<a id="linux-ubuntu" class="xliff"></a>
Öppna terminalen och navigera till den katalog där du vill skapa Python-skriptet. Ange följande kommandon för att installera **Microsoft ODBC-drivrutin för Linux** och **pyodbc**. pyodbc använder Microsoft ODBC Driver på Linux för att ansluta till SQL-databaser.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### **Windows**
<a id="windows" class="xliff"></a>
Installera [Microsoft ODBC Driver 13,1](https://www.microsoft.com/download/details.aspx?id=53339) (uppgradera drivrutinen om du uppmanas). pyodbc använder Microsoft ODBC Driver på Linux för att ansluta till SQL-databaser. 

Installera sedan **pyodbc** med pip.

```cmd
pip install pyodbc==3.1.1
```

Instruktioner för att aktivera ett pip hittar du [här](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## Hämta anslutningsinformation
<a id="get-connection-information" class="xliff"></a>

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet, databasnamnet och inloggningsinformationen i nästa procedurer.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. Granska serverns fullständiga namn på sidan **Översikt** för databasen, se bilden nedan. Om du hovrar över servernamnet visas alternativet **Kopiera genom att klicka**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Om du glömmer inloggningsinformationen för din server öppnar du serversidan i SQL Database. Där ser du administratörsnamnet för servern och kan återställa lösenordet vid behov.     
   
## Välj data
<a id="select-data" class="xliff"></a>

Använd följande kod för att söka efter de 20 främsta produkterna med funktionen [pyodbc.connect](https://github.com/mkleehammer/pyodbc/wiki) och transact-SQL-instruktionen [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Funktionen [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) används för att hämta en resultatmängd från en fråga mot SQL-databasen. Den här funktionen accepterar en fråga och returnerar en resultatuppsättning som kan upprepas med hjälp av **cursor.fetchone()**. Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## Infoga data
<a id="insert-data" class="xliff"></a>
Använd följande kod för att infoga en ny produkt i tabellen SalesLT.Product med funktionen [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) och transact-SQL-instruktionen [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## Uppdatera data
<a id="update-data" class="xliff"></a>
Med följande kod uppdaterar du den nya produkt du tidigare lade till med funktionen [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) och Transact-SQL-instruktionen [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) . Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

## Ta bort data
<a id="delete-data" class="xliff"></a>
Med följande kod raderar du den nya produkt du tidigare lade till med funktionen [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) och Transact-SQL-instruktionen [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) . Ersätt server, databas, användarnamn och lösenordsparametrar med värden som du angav när du skapade databasen med exempeldata AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## Nästa steg
<a id="next-steps" class="xliff"></a>

- [Utforma din första Azure SQL Database](sql-database-design-first-database.md)
- [Microsoft Python-drivrutiner för SQLServer](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python Developer Center](https://azure.microsoft.com/develop/python/?v=17.23h)


