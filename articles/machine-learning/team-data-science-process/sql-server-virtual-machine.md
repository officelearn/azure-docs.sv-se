---
title: Utforska data i en SQL Server-dator – Team Data Science Process
description: Utforska + bearbeta data och generera funktioner med python eller SQL i en SQL Server virtuell dator på Azure.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718488"
---
# <a name="heading"></a>Bearbeta data i SQL Server virtuell dator på Azure
Det här dokumentet beskriver hur du utforska data och generera funktioner för data som lagras i en SQL Server-VM på Azure. Det här målet kan utföras av data datatransformering med SQL eller genom att använda ett programmeringsspråk som python.

> [!NOTE]
> SQL-uttryck för exemplet i det här dokumentet förutsätter att data är i SQL Server. Om det inte finns på cloud data science process kartan för att lära dig hur du flyttar dina data till SQL Server.
> 
> 

## <a name="SQL"></a>Använda SQL
Vi beskriver data följande wrangling uppgifter i det här avsnittet med hjälp av SQL:

1. [Data utforskning](#sql-dataexploration)
2. [Generering av funktioner](#sql-featuregen)

### <a name="sql-dataexploration"></a>Data utforskning
Här följer några exempel SQL-skript som kan användas för att utforska datalager i SQL Server.

> [!NOTE]
> I ett praktiskt exempel kan du använda [NYC taxi-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) och REFERERA till IPNB- [NYC data datatransformering med hjälp av IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en slut punkt till slut punkt.
> 
> 

1. Få ett värde för observationer per dag
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Hämta nivåer i en kategoriska kolumn
   
    `select  distinct <column_name> from <databasename>`
3. Hämta antalet nivåer i kombination med två kategoriska kolumner 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Hämta fördelningen för numeriska kolumner
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Generering av funktioner
I det här avsnittet beskrivs olika sätt att generera funktioner med hjälp av SQL:  

1. [Antal baserade funktions generationer](#sql-countfeature)
2. [Generering av diskretisering-funktioner](#sql-binningfeature)
3. [Distribuera funktionerna från en enda kolumn](#sql-featurerollout)

> [!NOTE]
> När du har genererat ytterligare funktioner kan du lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primär nyckel, som kan kopplas till den ursprungliga tabellen. 
> 
> 

### <a name="sql-countfeature"></a>Antal baserade funktions generationer
I följande exempel visar två sätt att generera antal funktioner. Den första metoden använder villkorlig summan och den andra metoden använder ”where”-satsen. De här resultaten kan sedan kopplas till den ursprungliga tabellen (med primär nyckel kolumner) för att räkna funktioner tillsammans med ursprungliga data.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Generering av diskretisering-funktioner
I följande exempel visas hur du skapar binned funktioner av diskretisering (med fem lagerplatser) en numerisk kolumn som kan användas som en funktion i stället:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Distribuera funktionerna från en enda kolumn
I det här avsnittet visar vi hur du ska distribuera en enda kolumn i en tabell för att generera ytterligare funktioner. I exemplet förutsätter vi att det finns en latitud och longitud kolumn i tabellen som du vill generera funktioner.

Här är en kort introduktion till plats data för latitud/longitud (omursprungs från StackOverflow [hur du mäter precisionen för latitud och longitud?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Den här vägledningen är användbar för att förstå innan du inkluderar platsen som en eller flera funktioner:

* Tecknet talar om för oss om vi är norr eller söder, Öst eller Väst i världen.
* Ett annat värde än noll hundratals siffra talar om för oss att vi använder longitud, latitud inte!
* Tio siffror ger möjlighet att cirka 1 000 kilometer. Det ger oss användbar information om vilka kontinent eller miljöer är vi på.
* Enheter siffra (en decimal grad) ger en position upp till 111 kilometer (60 sjömil, ungefär 69 miles). Det kan berätta ungefär vilket tillstånd, land eller region du befinner dig i.
* En decimal är värt att upp till 11.1 km: Det kan skilja positionen för en stor stad från en närliggande stora stad.
* Andra decimal är värt att upp till 1.1 km: Det kan skilja ett village från nästa.
* Den tredje decimalen är värt att upp till 110 m: kan identifiera ett stort agricultural fält eller institutionella campus.
* Fjärde decimal är värt att upp till 11 m: mark kan identifiera. Det är jämförbar vanliga riktighet en okorrigerad GPS-enhet utan störningar.
* Den femte decimalen är värt att upp till 1.1 m: den särskiljer träd från varandra. Precision på den här nivån med kommersiella GPS-enheter kan bara ske med differentiell korrigering.
* Den sjätte decimalen är värt att upp till 0.11 m: du kan använda detta strållayoutmotor strukturer i detalj, för att utforma landskap, för att skapa vägar. Det bör vara mer än tillräckligt bra för att spåra förflyttningar av glaciers och vattendrag. Detta kan uppnås genom att utföra painstaking åtgärder med GPS, till exempel differentially korrigerad GPS.

Platsinformationen kan vara trädmodell på följande sätt att separera region, plats och information om ort. Du kan också anropa en REST-slutpunkt, till exempel Bing Maps-API: n, som finns på [hitta en plats genom att peka](https://msdn.microsoft.com/library/ff701710.aspx) på Hämta region/distrikts information.

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

Dessa funktioner för plats-baserade kan ytterligare används för att generera ytterligare antal funktioner enligt beskrivningen ovan. 

> [!TIP]
> Du kan via programmering Infoga posterna språk du föredrar. Du kan behöva infoga data i segment för att förbättra Skriv effektiviteten (ett exempel på hur du gör detta med hjälp av pyodbc finns i [ett HelloWorld-exempel för att få åtkomst till SQLServer med python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Ett annat alternativ är att infoga data i databasen med hjälp av [BCP-verktyget](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Ansluter till Azure Machine Learning
Funktionen nygenererade kan läggas till som en kolumn i en befintlig tabell eller lagras i en ny tabell och anslutits med den ursprungliga tabellen för machine learning. Funktioner kan genereras eller nås om de redan har skapats, med hjälp av modulen [Importera data][import-data] i Azure Machine Learning som visas nedan:

![azureml-läsare][1] 

## <a name="python"></a>Använda ett programmeringsspråk som python
Använda python för att utforska data och generera funktioner när data är i SQL Server liknar bearbetning av data i Azure Blob med hjälp av python som dokumenterade i [bearbeta Azure blob-data i din data vetenskaps miljö](data-blob.md). Läs in data från databasen till en Pandas data ram för mer bearbetning. Vi dokumenterar hur du ansluter till databasen och läser in data till dataram i det här avsnittet.

Följande formatet för anslutningssträngen kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (Ersätt servername, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas-biblioteket](https://pandas.pydata.org/) i python innehåller en omfattande uppsättning data strukturer och data analys verktyg för data manipulation för python-programmering. Koden nedan läser resultatet som returneras från en SQL Server-databas till en dataram Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu kan du arbeta med data ramen Pandas som beskrivs i artikeln [bearbeta Azure blob-data i din data science-miljö](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Azure Data Science i åtgärden exemplet
Ett exempel på en fullständig genom gång av Azure Data Science-processen med hjälp av en offentlig data uppsättning finns [i Azure Data Science process](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

