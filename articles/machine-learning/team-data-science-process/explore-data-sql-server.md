---
title: Utforska data i SQL Server-dator i Azure | Microsoft Docs
description: Så här utforska data som lagras i en SQL Server-VM på Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ccbb3085-af9e-4ec2-9df2-15dcab261d05
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: d96852006377123f6e9d17c3ae5b79fe930c1e1c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836804"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Utforska data i en virtuell dator med SQL Server på Azure
Det här dokumentet beskriver hur du utforska data som lagras i en SQL Server-VM på Azure. Detta kan göras med data wrangling med hjälp av SQL eller med ett programmeringsspråk som Python.

Följande **menyn** länkar till avsnitt som beskriver hur du använder Verktyg för att utforska data från olika miljöer för lagring. Det här är ett steg i Cortana Analytics processen (CAP).

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

> [!NOTE]
> Exempel SQL-instruktioner i det här dokumentet förutsätter att data är i SQL Server. Om det inte finns molnet vetenskap processen mappa data att lära dig hur du flyttar dina data till SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Utforska SQL-data med SQL-skript
Här följer några exempel SQL-skript som kan användas för att utforska data lagras i SQL Server.

1. Hämta antal observationer per dag
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Hämta nivåerna i en kategoriska kolumn
   
    `select  distinct <column_name> from <databasename>`
3. Hämta antalet nivåer i kombination med två kategoriska kolumner 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Hämta distribution för numeriska kolumner
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> En praktisk t.ex, du kan använda den [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) och referera till IPNB med rubriken [NYC Data wrangling IPython anteckningsboken och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en slutpunkt till slutpunkt-hanteringspaketen.
> 
> 

## <a name="python"></a>Utforska SQL-data med Python
Använda Python att utforska data och generera funktioner om data finns i SQL Server är ungefär som att bearbeta data i Azure blob med Python, enligt beskrivningen i [processen Azure Blob-data i datavetenskap miljön](data-blob.md). Data måste läsas in från databasen till en pandas DataFrame och sedan behandlas vidare. Vi dokumentera processen för att ansluta till databasen och läsa in data i DataFrame i det här avsnittet.

Följande connection-strängformat kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (Ersätt servername, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Den [Pandas biblioteket](http://pandas.pydata.org/) i Python ger en omfattande uppsättning datastrukturer och verktyg för analys av data till datamanipulering för Python-programmering. Följande kod läser resultatet som returneras från en SQL Server-databas till en Pandas data ram:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Nu kan du arbeta med Pandas DataFrame som beskrivs i avsnittet [processen Azure Blob-data i datavetenskap miljön](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Team vetenskap av data i åtgärden exempel
En slutpunkt till slutpunkt genomgången exempel processens Cortana Analytics med hjälp av en offentlig dataset finns [Team datavetenskap Process i praktiken: använder SQL Server](sql-walkthrough.md).

