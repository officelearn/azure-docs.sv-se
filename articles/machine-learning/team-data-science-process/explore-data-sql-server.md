---
title: Utforska data i virtuell SQL Server - Team Data Science Process
description: Så här utforskar du data som lagras i en VIRTUELL SQL Server på Azure med SQL eller ett programmeringsspråk som Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ae8c7c43ecbf9bc625e1e46be3e2c71c8d57b6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720103"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Utforska data i en virtuell dator med SQL Server på Azure

Den här artikeln beskriver hur du utforskar data som lagras i en VIRTUELL SQL Server på Azure. Använd SQL eller Python för att undersöka data.

Den här uppgiften är ett steg i [teamdatavetenskapsprocessen](overview.md).

> [!NOTE]
> Exempel på SQL-uttryck i det här dokumentet förutsätter att data finns i SQL Server. Om så inte är fallet läser du molndatascience-processöversikten för att lära dig hur du flyttar dina data till SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Utforska SQL-data med SQL-skript
Här är några exempel på SQL-skript som kan användas för att utforska datalager i SQL Server.

1. Få antalet observationer per dag
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Hämta nivåerna i en kategorisk kolumn
   
    `select  distinct <column_name> from <databasename>`
3. Hämta antalet nivåer i kombination med två kategoriska kolumner 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Hämta fördelningen för numeriska kolumner
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Ett praktiskt exempel kan du använda [NYC Taxi-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) och referera till IPNB-titeln [NYC Data som käbbel med IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en heltäckande genomgång.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Utforska SQL-data med Python
Använda Python för att utforska data och generera funktioner när data finns i SQL Server liknar bearbetning av data i Azure blob med Python, som dokumenteras i [Process Azure Blob-data i din datavetenskapsmiljö](data-blob.md). Ladda data från databasen i en pandas DataFrame och sedan kan bearbetas ytterligare. Vi dokumenterar processen att ansluta till databasen och läsa in data i DataFrame i det här avsnittet.

Följande anslutningssträngformat kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (ersätt servernamn, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas-biblioteket](https://pandas.pydata.org/) i Python tillhandahåller en omfattande uppsättning datastrukturer och dataanalysverktyg för datamanipulering för Python-programmering. Följande kod läser resultaten som returneras från en SQL Server-databas till en Pandas-dataram:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu kan du arbeta med Pandas DataFrame som beskrivs i ämnet [Process Azure Blob-data i din datavetenskapsmiljö](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Exempel på teamdatavetenskapsprocess i åtgärd
Ett exempel på en genomgång av Cortana Analytics-processen med hjälp av en offentlig datauppsättning finns [i Åtgärden Team Data Science i praktiken: använda SQL Server](sql-walkthrough.md).

