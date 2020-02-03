---
title: Skapa funktioner i SQL Server med SQL och Python - Team Data Science Process
description: Skapa funktioner för data som lagras i en SQL Server-VM på Azure med SQL och Python – en del av Team Data Science Process.
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
ms.openlocfilehash: 58fa98005d7d89e84404d99cf4f55e456fd91f21
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721752"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Skapa funktioner för data i SQL Server med SQL och Python
Det här dokumentet visar hur du skapar funktioner för data som lagras i en SQL Server-VM på Azure som hjälper algoritmer som Lär dig mer effektivt från data. Du kan använda SQL-databaser eller ett programmeringsspråk som Python för att åstadkomma detta. Båda metoderna är visas här.

Den här uppgiften är ett steg i [TDSP (Team data science process)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

> [!NOTE]
> Ett praktiskt exempel är att du kan se [NYC taxi-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) och REFERERA till IPNB- [NYC data datatransformering med hjälp av IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en genom gång från slut punkt till slut punkt.
> 
> 

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver instruktioner, se [skapa ett Azure Storage konto](../../storage/common/storage-account-create.md)
* Lagras dina data i SQL Server. Om du inte har gjort det, se [Flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md) för instruktioner om hur du flyttar data dit.

## <a name="sql-featuregen"></a>Generering av funktioner med SQL
I det här avsnittet beskrivs olika sätt att generera funktioner med hjälp av SQL:  

* [Antal baserade funktions generationer](#sql-countfeature)
* [Generering av diskretisering-funktioner](#sql-binningfeature)
* [Distribuera funktionerna från en enda kolumn](#sql-featurerollout)

> [!NOTE]
> När du har genererat ytterligare funktioner kan du lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primär nyckel, som kan kopplas till den ursprungliga tabellen.
> 
> 

### <a name="sql-countfeature"></a>Antal baserade funktions generationer
Det här dokumentet visar två sätt att generera antal funktioner. Den första metoden använder villkorlig summan och den andra metoden använder ”where”-satsen. De här nya funktionerna kan sedan anslutas till den ursprungliga tabellen (med primär nyckel kolumner) för att räkna funktioner tillsammans med ursprungliga data.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Generering av diskretisering-funktioner
I följande exempel visas hur du skapar binned funktioner av diskretisering (med fem lagerplatser) en numerisk kolumn som kan användas som en funktion i stället:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Distribuera funktionerna från en enda kolumn
I det här avsnittet visar vi hur du ska distribuera en enda kolumn i en tabell för att generera ytterligare funktioner. I exemplet förutsätter vi att det finns en latitud och longitud kolumn i tabellen som du vill generera funktioner.

Här är en kort introduktion till plats data för latitud/longitud (omursprungs från StackOverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Här följer några användbara saker att känna till om platsdata innan du skapar funktioner från fältet:

* Inloggningssidan anger om vi är norr eller söder, östra eller Väst i världen.
* Ett annat värde än noll hundratals siffra anger longitud, latitud inte används.
* Tio siffror ger möjlighet att cirka 1 000 kilometer. Det ger användbar information om vilka kontinent eller miljöer är vi på.
* Enheter siffra (en decimal grad) ger en position upp till 111 kilometer (60 sjömil, ungefär 69 miles). Det indikerar, ungefär, vilket stort tillstånd eller land/region som vi befinner sig i.
* En decimal är värt att upp till 11.1 km: Det kan skilja positionen för en stor stad från en närliggande stora stad.
* Andra decimal är värt att upp till 1.1 km: Det kan skilja ett village från nästa.
* Den tredje decimalen är värt att upp till 110 m: kan identifiera ett stort agricultural fält eller institutionella campus.
* Fjärde decimal är värt att upp till 11 m: mark kan identifiera. Det är jämförbar vanliga riktighet en okorrigerad GPS-enhet utan störningar.
* Den femte decimalen är värt att upp till 1.1 m: den särskiljer träd från varandra. Precision på den här nivån med kommersiella GPS-enheter kan bara ske med differentiell korrigering.
* Den sjätte decimalen är värd för upp till 0,11 m: du kan använda den här nivån för att utforma strukturer i detalj, för att utforma landskap, bygga vägar. Det bör vara mer än tillräckligt bra för att spåra förflyttningar av glaciers och vattendrag. Det här målet kan uppnås genom att vidta painstaking-åtgärder med GPS, till exempel Differentiellt korrigerat GPS.

Platsinformationen kan vara trädmodell genom att ange region, plats och information om ort. En gång kan också anropa en REST-slutpunkt, till exempel Bing Maps API (se `https://msdn.microsoft.com/library/ff701710.aspx` för att hämta region/distrikts information).

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
> Du kan via programmering Infoga posterna språk du föredrar. Du kan behöva infogar data i segment för att förbättra effektiviteten för skrivning. [Här är ett exempel på hur du gör detta med hjälp av pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Ett annat alternativ är att infoga data i databasen med hjälp av [BCP-verktyget](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Ansluter till Azure Machine Learning
Funktionen nygenererade kan läggas till som en kolumn i en befintlig tabell eller lagras i en ny tabell och anslutits med den ursprungliga tabellen för machine learning. Funktioner kan genereras eller nås om de redan har skapats, med hjälp av modulen [Importera data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) i Azure ml som visas nedan:

![Azure ML-läsare](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Använda ett programmeringsspråk som python
Använda Python för att skapa funktioner om data finns i SQL Server liknar bearbetning av data i Azure-blob med hjälp av Python. För jämförelse finns mer information [i bearbeta Azure blob-data i din data science-miljö](data-blob.md). Läs in data från databasen till en pandas dataram att bearbeta det ytterligare. Processen för att ansluta till databasen och läser in data till dataramen dokumenteras i det här avsnittet.

Följande formatet för anslutningssträngen kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (Ersätt servername, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas-biblioteket](https://pandas.pydata.org/) i python innehåller en omfattande uppsättning data strukturer och data analys verktyg för data manipulation för python-programmering. Följande kod läser resultatet som returneras från en SQL Server-databas till en dataram Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu kan du arbeta med data ramen Pandas som beskrivs i avsnittet [skapa funktioner för Azure Blob Storage-data med hjälp av Panda](create-features-blob.md).

