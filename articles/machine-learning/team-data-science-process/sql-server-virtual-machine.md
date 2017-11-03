---
title: Utforska data i en SQL Server-dator i Azure | Microsoft Docs
description: "Utforska data och skapa funktioner i en virtuell dator i SQL Server på Azure"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: 
ms.assetid: 3949fb2c-ffab-49fb-908d-27d5e42f743b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: fashah;garye;bradsev
ms.openlocfilehash: 063709a22540e22d1eb6f1c6a6ff777e95f6a29a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="heading"></a>Bearbeta Data i SQL Server-dator på Azure
Det här dokumentet innehåller information om hur att utforska data och generera data som lagras i en SQL Server-VM på Azure-funktioner. Detta kan göras med data wrangling med hjälp av SQL eller med ett programmeringsspråk som Python.

> [!NOTE]
> Exempel SQL-instruktioner i det här dokumentet förutsätter att data är i SQL Server. Om det inte finns molnet vetenskap processen mappa data att lära dig hur du flyttar dina data till SQL Server.
> 
> 

## <a name="SQL"></a>Med SQL
Vi beskriver data följande wrangling uppgifter i det här avsnittet med hjälp av SQL:

1. [Datagranskning](#sql-dataexploration)
2. [Funktionen Generation](#sql-featuregen)

### <a name="sql-dataexploration"></a>Datagranskning
Här följer några exempel SQL-skript som kan användas för att utforska data lagras i SQL Server.

> [!NOTE]
> En praktisk t.ex, du kan använda den [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) och referera till IPNB med rubriken [NYC Data wrangling IPython anteckningsboken och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en slutpunkt till slutpunkt-hanteringspaketen.
> 
> 

1. Hämta antal observationer per dag
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Hämta nivåerna i en kategoriska kolumn
   
    `select  distinct <column_name> from <databasename>`
3. Hämta antalet nivåer i kombination med två kategoriska kolumner 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Hämta distribution för numeriska kolumner
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Funktionen Generation
I det här avsnittet beskrivs olika sätt att skapa funktioner med hjälp av SQL:  

1. [Antal baserat funktionen Generation](#sql-countfeature)
2. [Diskretisering funktionen Generation](#sql-binningfeature)
3. [Lansera funktionerna från en enda kolumn](#sql-featurerollout)

> [!NOTE]
> När du skapar ytterligare funktioner, kan du lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primärnyckel som kan kopplas till den ursprungliga tabellen. 
> 
> 

### <a name="sql-countfeature"></a>Antal baserat funktionen Generation
I följande exempel visas två sätt genererar antal funktioner. Den första metoden använder Villkorlig summering och den andra metoden använder uttrycket 'where'. Dessa kan sedan kopplas till den ursprungliga tabellen (med primärnyckelkolumnerna) om du vill att antalet funktioner tillsammans med den ursprungliga informationen.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Diskretisering funktionen Generation
I följande exempel visas hur du skapar binned funktioner av diskretisering (med fem lagerplatser) en numerisk kolumn som kan användas som en funktion i stället:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Lansera funktionerna från en enda kolumn
I det här avsnittet visar vi hur du lanserar en kolumn i en tabell för att generera ytterligare funktioner. Exemplet förutsätter att det finns en latitud och longitud kolumn i tabellen som du vill generera funktioner.

Här är en kort introduktion om latitud/longitud platsdata (resurstilldelas från stackoverflow [så att mäta riktighet latitud och longitud?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Detta är användbart för att förstå innan featurizing Platsfältet:

* Tecknet talar om för oss om vi är norr eller söder, Öst eller Väst på jorden.
* Ett annat värde än noll hundratals siffra talar om för oss att vi använder longitud inte latitud!
* Flera siffra ger möjlighet att ca 1 000 kilometer. Det ger oss användbar information om vilka kontinent eller oceanen vi finns på.
* Enheter siffra (en decimal grad) ger en position upp till 111 kilometer (60 sjömil, om 69 miles). Det kan berätta ungefär vad eller det stora land vi finns i.
* En decimal är värd upp till 11.1 km: den kan skilja positionen för en stor ort från en närliggande stora stad.
* Till andra decimaltecknet som är värd upp till 1.1 km: den kan skilja en village från nästa.
* Den tredje decimaltecknet är högst 110 m: den kan identifiera en stor jordbruket institutionella plats.
* Den fjärde decimaltecknet är upp till 11 m: mark kan identifiera. Det är jämförbar med vanliga precision på en GPS-enhet som inte har korrigerats utan störningar.
* Femte decimaltecknet är upp till 1.1 m: den särskiljer träd från varandra. Precision på den här nivån med kommersiella GPS-enheter kan endast ske med differentiell korrigering.
* Sjätte decimaltecknet är värt upp till 0.11 m: kan du använda den för att utforma strukturer i detalj för att utforma landskap, skapa vägar. Det bör vara mer än tillräckligt bra för spårning av glaciers och floder. Detta kan uppnås genom att göra painstaking åtgärder med GPS, till exempel differentially korrigerade GPS.

Platsinformationen kan vara featurized på följande sätt att skilja ut region, plats och information om ort. Observera att du kan också anropa en REST-slutpunkt, till exempel Bing Maps API finns på [hitta en plats med en punkt](https://msdn.microsoft.com/library/ff701710.aspx) att hämta information om region/distrikt.

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

Dessa funktioner för plats-baserad kan användas mer att generera ytterligare antal funktioner som tidigare beskrivits. 

> [!TIP]
> Du kan via programmering Infoga posterna med ditt språk. Du kan behöva infoga data i segment att förbättra effektiviteten för skrivning (ett exempel på hur du gör detta med hjälp av pyodbc finns [A HelloWorld exempel för att få åtkomst till SQL Server med python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Ett annat alternativ är att infoga data i databasen med hjälp av [BCP-verktyget](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Ansluta till Azure Machine Learning
Funktionen nyligen skapade kan läggas till som en kolumn i en befintlig tabell eller lagras i en ny tabell och kopplas till den ursprungliga tabellen för machine learning. Funktioner kan genereras eller komma åt om redan har skapats med hjälp av den [importera Data] [ import-data] modul i Azure Machine Learning enligt nedan:

![azureml-läsare][1] 

## <a name="python"></a>Med hjälp av programmeringsspråk som Python
Använder Python att utforska data och generera funktioner om data finns i SQL Server liknar bearbetning av data i Azure blob använder Python enligt beskrivningen i [processen Azure Blob-data i datavetenskap miljön](data-blob.md). Data måste läsas in från databasen till en pandas data ram och sedan behandlas vidare. Vi dokumentera processen för att ansluta till databasen och läsa in data i data-ram i det här avsnittet.

Följande connection-strängformat kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (Ersätt servername, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Den [Pandas biblioteket](http://pandas.pydata.org/) i Python ger en omfattande uppsättning datastrukturer och verktyg för analys av data till datamanipulering för Python-programmering. Koden nedan läser resultatet som returneras från en SQL Server-databas till en Pandas data ram:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Nu kan du arbeta med Pandas dataramen som beskrivs i artikeln [processen Azure Blob-data i datavetenskap miljön](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Azure datavetenskap i åtgärden exempel
En slutpunkt till slutpunkt genomgången exempel på Azure vetenskap av data med hjälp av en offentlig dataset finns [Azure datavetenskap hur fungerar](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

