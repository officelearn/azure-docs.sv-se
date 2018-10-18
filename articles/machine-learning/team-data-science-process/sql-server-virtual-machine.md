---
title: Utforska data i en SQL Server-dator på Azure | Microsoft Docs
description: Utforska data och generera funktioner i en SQL Server-dator på Azure
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 3949fb2c-ffab-49fb-908d-27d5e42f743b
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: deguhath
ms.openlocfilehash: 350e634ce87e2f61352beb194b4468828aea60c6
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394962"
---
# <a name="heading"></a>Bearbeta Data i SQL Server-dator på Azure
Det här dokumentet beskriver hur du utforska data och generera funktioner för data som lagras i en SQL Server-VM på Azure. Detta kan göras med Datatransformering med hjälp av SQL eller med ett programmeringsspråk som Python.

> [!NOTE]
> SQL-uttryck för exemplet i det här dokumentet förutsätter att data är i SQL Server. Om det inte finns på cloud data science process kartan för att lära dig hur du flyttar dina data till SQL Server.
> 
> 

## <a name="SQL"></a>Med hjälp av SQL
Vi beskriver data följande wrangling uppgifter i det här avsnittet med hjälp av SQL:

1. [Datautforskning](#sql-dataexploration)
2. [Funktionen Generation](#sql-featuregen)

### <a name="sql-dataexploration"></a>Datautforskning
Här följer några exempel SQL-skript som kan användas för att utforska datalager i SQL Server.

> [!NOTE]
> En praktiska exempel: du kan använda den [NYC Taxi datauppsättning](http://www.andresmh.com/nyctaxitrips/) och referera till IPNB benämnt [NYC Datatransformering med IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) en slutpunkt till slutpunkt genomgång.
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

### <a name="sql-featuregen"></a>Funktionen Generation
I det här avsnittet beskrivs olika sätt att generera funktioner med hjälp av SQL:  

1. [Antal baserat funktionen Generation](#sql-countfeature)
2. [Datagruppering funktionen Generation](#sql-binningfeature)
3. [Lansera funktionerna från en enda kolumn](#sql-featurerollout)

> [!NOTE]
> När du har genererat ytterligare funktioner kan du lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primär nyckel, som kan kopplas till den ursprungliga tabellen. 
> 
> 

### <a name="sql-countfeature"></a>Antal baserat funktionen Generation
I följande exempel visar två sätt att generera antal funktioner. Den första metoden använder villkorlig summan och den andra metoden använder ”where”-satsen. Dessa kan sedan kopplas till den ursprungliga tabellen (med primärnyckelskolumnerna) om du vill att antal funktioner tillsammans med den ursprungliga informationen.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Datagruppering funktionen Generation
I följande exempel visas hur du skapar binned funktioner av diskretisering (med fem lagerplatser) en numerisk kolumn som kan användas som en funktion i stället:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Lansera funktionerna från en enda kolumn
I det här avsnittet visar vi hur du ska distribuera en enda kolumn i en tabell för att generera ytterligare funktioner. I exemplet förutsätter vi att det finns en latitud och longitud kolumn i tabellen som du vill generera funktioner.

Här är en kort introduktion på latitud/longitud platsdata (resurstilldelas från stackoverflow [hur du använder det arbete du utfört latitud och longitud?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Detta är användbart att förstå innan du featurizing Platsfältet:

* Tecknet talar om för oss om vi är norr eller söder, Öst eller Väst i världen.
* Ett annat värde än noll hundratals siffra talar om för oss att vi använder longitud, latitud inte!
* Tio siffror ger möjlighet att cirka 1 000 kilometer. Det ger oss användbar information om vilka kontinent eller miljöer är vi på.
* Enheter siffra (en decimal grad) ger en position upp till 111 kilometer (60 sjömil, ungefär 69 miles). Det kan berätta för oss ungefär vilka stora region eller land som det är en.
* En decimal är värt att upp till 11.1 km: Det kan skilja positionen för en stor stad från en närliggande stora stad.
* Andra decimal är värt att upp till 1.1 km: Det kan skilja ett village från nästa.
* Den tredje decimalen är värt att upp till 110 m: kan identifiera ett stort agricultural fält eller institutionella campus.
* Fjärde decimal är värt att upp till 11 m: mark kan identifiera. Det är jämförbar vanliga riktighet en okorrigerad GPS-enhet utan störningar.
* Den femte decimalen är värt att upp till 1.1 m: den särskiljer träd från varandra. Precision på den här nivån med kommersiella GPS-enheter kan bara ske med differentiell korrigering.
* Den sjätte decimalen är värt att upp till 0.11 m: du kan använda detta strållayoutmotor strukturer i detalj, för att utforma landskap, för att skapa vägar. Det bör vara mer än tillräckligt bra för att spåra förflyttningar av glaciers och vattendrag. Detta kan uppnås genom att utföra painstaking åtgärder med GPS, till exempel differentially korrigerad GPS.

Platsinformationen kan vara trädmodell på följande sätt att separera region, plats och information om ort. Observera att du kan också anropa en REST-slutpunkt, till exempel Bing Maps-API som är tillgängliga på [hitta en plats med punkt](https://msdn.microsoft.com/library/ff701710.aspx) att hämta information om region/distrikt.

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
> Du kan via programmering Infoga posterna språk du föredrar. Du kan behöva infogar data i segment för att förbättra effektiviteten för skrivning (ett exempel på hur du gör detta med hjälp av pyodbc finns i [A HelloWorld-exemplet för att komma åt SQLServer med python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Ett annat alternativ är att infoga data i databasen med den [BCP för](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Ansluta till Azure Machine Learning
Funktionen nygenererade kan läggas till som en kolumn i en befintlig tabell eller lagras i en ny tabell och anslutits med den ursprungliga tabellen för machine learning. Funktioner som kan genereras eller nås om redan har skapat med hjälp av den [importera Data] [ import-data] modul i Azure Machine Learning enligt nedan:

![azureml-läsare][1] 

## <a name="python"></a>Med hjälp av ett programmeringsspråk som Python
Med hjälp av Python att utforska data och generera funktioner om data finns i SQL Server är liknande för bearbetning av data i Azure-blob med hjälp av Python som beskrivs i [processen Azure Blob-data i miljön data science](data-blob.md). Data måste läsas in från databasen till en dataram pandas och sedan kan bearbetas ytterligare. Vi dokumenterar hur du ansluter till databasen och läser in data till dataram i det här avsnittet.

Följande formatet för anslutningssträngen kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (Ersätt servername, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Den [Pandas biblioteket](http://pandas.pydata.org/) tillhandahåller en omfattande uppsättning datastrukturer och verktyg för analys av data för datamanipulering för Python-programmering i Python. Koden nedan läser resultatet som returneras från en SQL Server-databas till en dataram Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Nu kan du arbeta med Pandas dataram som beskrivs i artikeln [processen Azure Blob-data i miljön data science](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Azure Data Science i åtgärden exemplet
En slutpunkt till slutpunkt genomgång av exempel på Azure Data Science Process använder en offentlig datauppsättning finns [Azure Data Science Process i praktiken](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

