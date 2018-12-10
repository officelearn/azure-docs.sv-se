---
title: Utforska data i SQL Server-dator – Team Data Science Process
description: Så här att utforska data som lagras i en SQL Server-VM på Azure med SQL- eller ett programmeringsspråk som Python.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 45b842f7ff136f2d53b7724c11141f6b9104ebd7
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140123"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Utforska data i en virtuell dator med SQL Server på Azure

Den här artikeln beskriver hur du utforska data som lagras i en SQL Server-VM på Azure. Detta kan göras med Datatransformering med hjälp av SQL eller med ett programmeringsspråk som Python.

Den här uppgiften är ett steg i den [Team Data Science Process](overview.md).

> [!NOTE]
> SQL-uttryck för exemplet i det här dokumentet förutsätter att data är i SQL Server. Om det inte finns på cloud data science process kartan för att lära dig hur du flyttar dina data till SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Utforska SQL-data med SQL-skript
Här följer några exempel SQL-skript som kan användas för att utforska datalager i SQL Server.

1. Få ett värde för observationer per dag
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Hämta nivåer i en kategoriska kolumn
   
    `select  distinct <column_name> from <databasename>`
3. Hämta antalet nivåer i kombination med två kategoriska kolumner 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Hämta fördelningen för numeriska kolumner
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> En praktiska exempel: du kan använda den [NYC Taxi datauppsättning](http://www.andresmh.com/nyctaxitrips/) och referera till IPNB benämnt [NYC Datatransformering med IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) en slutpunkt till slutpunkt genomgång.
> 
> 

## <a name="python"></a>Utforska SQL-data med Python
Använda Python för att utforska data och generera funktioner om data finns i SQL Server är liknande för bearbetning av data i Azure-blob med hjälp av Python, enligt beskrivningen i [processen Azure Blob-data i miljön data science](data-blob.md). Data måste läsas in från databasen till en pandas-DataFrame och sedan kan bearbetas ytterligare. Vi dokumenterar hur du ansluter till databasen och läser in data till dataram i det här avsnittet.

Följande formatet för anslutningssträngen kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (Ersätt servername, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Den [Pandas biblioteket](http://pandas.pydata.org/) tillhandahåller en omfattande uppsättning datastrukturer och verktyg för analys av data för datamanipulering för Python-programmering i Python. Följande kod läser resultatet som returneras från en SQL Server-databas till en dataram Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Nu kan du arbeta med Pandas-DataFrame som beskrivs i avsnittet [processen Azure Blob-data i miljön data science](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Team Data Science Process i åtgärden exemplet
En slutpunkt till slutpunkt genomgång av exempel på Cortana Analytics-processen använder en offentlig datauppsättning finns [Team Data Science Process i praktiken: med SQL Server](sql-walkthrough.md).

