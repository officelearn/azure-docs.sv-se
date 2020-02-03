---
title: Utforska data i SQL Server-dator – Team Data Science Process
description: Så här att utforska data som lagras i en SQL Server-VM på Azure med SQL- eller ett programmeringsspråk som Python.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720103"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Utforska data i en virtuell dator med SQL Server på Azure

Den här artikeln beskriver hur du utforska data som lagras i en SQL Server-VM på Azure. Använd SQL eller python för att granska data.

Den här uppgiften är ett steg i [processen för team data vetenskap](overview.md).

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
> I ett praktiskt exempel kan du använda [NYC taxi-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) och REFERERA till IPNB- [NYC data datatransformering med hjälp av IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en slut punkt till slut punkt.
> 
> 

## <a name="python"></a>Utforska SQL-data med python
Använda python för att utforska data och generera funktioner när data är i SQL Server liknar bearbetning av data i Azure Blob med hjälp av python, enligt beskrivningen i [bearbeta Azure blob-data i din data vetenskaps miljö](data-blob.md). Läs in data från databasen till en Pandas-DataFrame och sedan kan du bearbeta dem ytterligare. Vi dokumenterar hur du ansluter till databasen och läser in data till dataram i det här avsnittet.

Följande formatet för anslutningssträngen kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (Ersätt servername, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas-biblioteket](https://pandas.pydata.org/) i python innehåller en omfattande uppsättning data strukturer och data analys verktyg för data manipulation för python-programmering. Följande kod läser resultatet som returneras från en SQL Server-databas till en dataram Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu kan du arbeta med Pandas-DataFrame som beskrivs i artikeln [bearbeta Azure blob-data i din data vetenskaps miljö](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Team Data Science Process i åtgärden exemplet
Ett exempel på en fullständig genom gång av Cortana Analytics-processen med hjälp av en offentlig data uppsättning finns [i processen team data science i praktiken: använda SQL Server](sql-walkthrough.md).

