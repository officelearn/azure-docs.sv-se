---
title: Utforska data i en virtuell SQL Server-dator - Team Data Science Process
description: Utforska + bearbeta data och generera funktioner med Python eller SQL i en virtuell SQL Server-dator på Azure.
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
ms.openlocfilehash: d3eb4d2faf58d1861fda9d04437f9f9530c77672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718488"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Bearbeta data i en virtuell dator med SQL Server på Azure
Det här dokumentet beskriver hur du utforskar data och genererar funktioner för data som lagras i en VIRTUELL SQL Server på Azure. Det här målet kan slutföras genom datakäbbel med SQL eller med hjälp av ett programmeringsspråk som Python.

> [!NOTE]
> Exempel på SQL-uttryck i det här dokumentet förutsätter att data finns i SQL Server. Om så inte är fallet läser du molndatascience-processöversikten för att lära dig hur du flyttar dina data till SQL Server.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>Använda SQL
Vi beskriver följande datakäbbel uppgifter i det här avsnittet med SQL:

1. [Data Exploration](#sql-dataexploration)
2. [Generering av funktioner](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Data Exploration
Här är några exempel på SQL-skript som kan användas för att utforska datalager i SQL Server.

> [!NOTE]
> Ett praktiskt exempel kan du använda [NYC Taxi-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) och referera till IPNB-titeln [NYC Data som käbbel med IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en heltäckande genomgång.
> 
> 

1. Få antalet observationer per dag
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Hämta nivåerna i en kategorisk kolumn
   
    `select  distinct <column_name> from <databasename>`
3. Hämta antalet nivåer i kombination med två kategoriska kolumner 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Hämta fördelningen för numeriska kolumner
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Generering av funktioner
I det här avsnittet beskriver vi olika sätt att generera funktioner med SQL:  

1. [Antal baserad funktionsgenerering](#sql-countfeature)
2. [Generering av binningsfunktion](#sql-binningfeature)
3. [Rulla ut funktionerna från en enda kolumn](#sql-featurerollout)

> [!NOTE]
> När du har genererat ytterligare funktioner kan du antingen lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primärnyckel, som kan sammanfogas med den ursprungliga tabellen. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Antal baserad funktionsgenerering
Följande exempel visar två sätt att generera räknefunktioner. Den första metoden använder villkorssum summan och den andra metoden använder "var"-satsen. Dessa resultat kan sedan sammanfogas med den ursprungliga tabellen (med hjälp av kolumner med primärnyckel) för att ha räknefunktioner bredvid de ursprungliga data.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Generering av binningsfunktion
I följande exempel visas hur du genererar lagerplatsfunktioner med binning (med fem lagerplatser) en numerisk kolumn som kan användas som en funktion i stället:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Rulla ut funktionerna från en enda kolumn
I det här avsnittet visar vi hur du skapar en enskild kolumn i en tabell för att generera ytterligare funktioner. Exemplet förutsätter att det finns en latitud eller longitudkolumn i tabellen som du försöker generera funktioner från.

Här är en kort primer på latitud / longitud platsdata (resurssätt från stackoverflow [Hur man mäter noggrannheten i latitud och longitud?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Den här vägledningen är användbar att förstå innan du inkluderar plats som en eller flera funktioner:

* Skylten talar om för oss om vi är norr eller söder, öster eller väster om världen.
* En nonzero hundratals siffra säger oss att vi använder longitud, inte latitud!
* Tiosiffran ger en position till cirka 1000 kilometer. Det ger oss användbar information om vilken kontinent eller hav vi befinner oss på.
* Enhetssiffran (en decimalgrad) ger en position på upp till 111 kilometer (60 nautiska miles, ca 69 miles). Det kan berätta ungefär vilken stat, land eller region du är i.
* Den första decimalen förlägger är värd upp till 11.1 km: det kan skilja placera av en stor stad från en neighboring stor stad.
* Den andra decimalen är värd upp till 1,1 km: den kan skilja en by från nästa.
* Den tredje decimalen är värd upp till 110 m: den kan identifiera ett stort jordbruksfält eller institutionellt campus.
* Den fjärde decimalen är värd upp till 11 m: den kan identifiera ett skifte. Det är jämförbart med den typiska noggrannheten hos en okorrigerad GPS-enhet utan störningar.
* Den femte decimalen är värd upp till 1,1 m: den skiljer träd från varandra. Noggrannhet till denna nivå med kommersiella GPS-enheter kan endast uppnås med differentialkorrigering.
* Den sjätte decimalen är värd upp till 0,11 m: du kan använda detta för att lägga ut strukturer i detalj, för att utforma landskap, bygga vägar. Det borde vara mer än tillräckligt bra för att spåra rörelser av glaciärer och floder. Detta kan uppnås genom att vidta noggranna åtgärder med GPS, såsom differentialkorrigerad GPS.

Platsinformationen kan visas på följande sätt, vilket skiljer region-, plats- och stadsinformation. Du kan också anropa en REST-slutpunkt, till exempel API för Bing Maps som är tillgängligt på [Hitta en plats efter punkt](https://msdn.microsoft.com/library/ff701710.aspx) för att få information om region/distrikt.

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Dessa platsbaserade funktioner kan användas ytterligare för att generera ytterligare räknefunktioner enligt beskrivningen tidigare. 

> [!TIP]
> Du kan infoga posterna programmässigt med ditt språk. Du kan behöva infoga data i segment för att förbättra skriveffektiviteten (ett exempel på hur du gör detta med pyodbc, se [Ett HelloWorld-exempel för att komma åt SQLServer med python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Ett annat alternativ är att infoga data i databasen med hjälp av [BCP-verktyget](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Ansluta till Azure Machine Learning
Den nygenererade funktionen kan läggas till som en kolumn i en befintlig tabell eller lagras i en ny tabell och sammanfogas med den ursprungliga tabellen för maskininlärning. Funktioner kan genereras eller nås om de redan har skapats med hjälp av modulen [Importera data][import-data] i Azure Machine Learning enligt nedan:

![azureml läsare][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Använda ett programmeringsspråk som Python
Att använda Python för att utforska data och generera funktioner när data finns i SQL Server liknar bearbetning av data i Azure-blob med Python som dokumenterat i [Process Azure Blob-data i din datavetenskapsmiljö](data-blob.md). Ladda data från databasen i en pandas dataram för mer bearbetning. Vi dokumenterar processen att ansluta till databasen och läsa in data i dataramen i det här avsnittet.

Följande anslutningssträngformat kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (ersätt servernamn, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas-biblioteket](https://pandas.pydata.org/) i Python tillhandahåller en omfattande uppsättning datastrukturer och dataanalysverktyg för datamanipulering för Python-programmering. Koden nedan läser resultaten som returneras från en SQL Server-databas till en Pandas-dataram:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu kan du arbeta med Pandas-dataramen som beskrivs i artikeln [Process Azure Blob-data i din datavetenskapsmiljö](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Exempel på Azure Data Science in Action
Ett exempel på en genomgång från slutna till slutna händer i Azure Data Science Process med hjälp av en offentlig datauppsättning finns [i Azure Data Science Process in Action](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

