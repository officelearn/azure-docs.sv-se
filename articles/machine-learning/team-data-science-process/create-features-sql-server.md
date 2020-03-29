---
title: Skapa funktioner i SQL Server med SQL och Python - Team Data Science Process
description: Generera funktioner för data som lagras i en VIRTUELL SQL Server på Azure med SQL och Python - en del av Team Data Science Process.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721752"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Skapa funktioner för data i SQL Server med SQL och Python
Det här dokumentet visar hur du genererar funktioner för data som lagras i en VIRTUELL SQL Server på Azure som hjälper algoritmer att lära sig mer effektivt från data. Du kan använda SQL eller ett programmeringsspråk som Python för att utföra den här uppgiften. Båda tillvägagångssätten visas här.

Den här uppgiften är ett steg i [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

> [!NOTE]
> Ett praktiskt exempel kan du läsa [NYC Taxi-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) och referera till IPNB med titeln [NYC Data som käbbel med IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en heltäckande genomgång.
> 
> 

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har:

* Skapade ett Azure-lagringskonto. Om du behöver instruktioner läser du [Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md)
* Lagrade dina data i SQL Server. Om du inte har det läser du [Flytta data till en Azure SQL-databas för Azure Machine Learning](move-sql-azure.md) för instruktioner om hur du flyttar data dit.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Funktionsgenerering med SQL
I det här avsnittet beskriver vi olika sätt att generera funktioner med SQL:  

* [Antal baserad funktionsgenerering](#sql-countfeature)
* [Generering av binningsfunktion](#sql-binningfeature)
* [Rulla ut funktionerna från en enda kolumn](#sql-featurerollout)

> [!NOTE]
> När du har genererat ytterligare funktioner kan du antingen lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primärnyckel, som kan sammanfogas med den ursprungliga tabellen.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Räkna baserad funktionsgenerering
Det här dokumentet visar två sätt att generera räknefunktioner. Den första metoden använder villkorssum summan och den andra metoden använder "var"-satsen. Dessa nya funktioner kan sedan sammanfogas med den ursprungliga tabellen (med hjälp av kolumner med primärnyckel) för att ha räknefunktioner bredvid de ursprungliga data.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Generering av binningsfunktion
I följande exempel visas hur du genererar lagerplatsfunktioner med binning (med fem lagerplatser) en numerisk kolumn som kan användas som en funktion i stället:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Rulla ut funktionerna från en enda kolumn
I det här avsnittet visar vi hur du skapar en enskild kolumn i en tabell för att generera ytterligare funktioner. Exemplet förutsätter att det finns en latitud eller longitudkolumn i tabellen som du försöker generera funktioner från.

Här är en kort primer på latitud / longitud platsdata (med resurser från stackoverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Här är några användbara saker att förstå om platsdata innan du skapar funktioner från fältet:

* Skylten visar om vi är norr eller söder, öster eller väster om världen.
* En ickezero hundratals siffra indikerar longitud, inte latitud används.
* Tiosiffran ger en position till cirka 1000 kilometer. Det ger användbar information om vilken kontinent eller hav vi befinner oss på.
* Enhetssiffran (en decimalgrad) ger en position på upp till 111 kilometer (60 nautiska miles, ca 69 miles). Det visar, ungefär, vilken stor stat eller land / region vi är i.
* Den första decimalen förlägger är värd upp till 11.1 km: det kan skilja placera av en stor stad från en neighboring stor stad.
* Den andra decimalen är värd upp till 1,1 km: den kan skilja en by från nästa.
* Den tredje decimalen är värd upp till 110 m: den kan identifiera ett stort jordbruksfält eller institutionellt campus.
* Den fjärde decimalen är värd upp till 11 m: den kan identifiera ett skifte. Det är jämförbart med den typiska noggrannheten hos en okorrigerad GPS-enhet utan störningar.
* Den femte decimalen är värd upp till 1,1 m: den skiljer träd från varandra. Noggrannhet till denna nivå med kommersiella GPS-enheter kan endast uppnås med differentialkorrigering.
* Den sjätte decimalen är värd upp till 0,11 m: du kan använda denna nivå för att lägga ut strukturer i detalj, för att utforma landskap, bygga vägar. Det borde vara mer än tillräckligt bra för att spåra rörelser av glaciärer och floder. Detta mål kan uppnås genom att vidta noggranna åtgärder med GPS, såsom differentialkorrigerad GPS.

Platsinformationen kan visas genom att separera region-, plats- och stadsinformation. En gång kan också anropa en REST-slutpunkt, `https://msdn.microsoft.com/library/ff701710.aspx` till exempel Bing Maps API (se för att hämta region/distriktsinformation).

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
> Du kan infoga posterna programmässigt med ditt språk. Du kan behöva infoga data i segment för att förbättra skriveffektiviteten. [Här är ett exempel på hur man gör detta med pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Ett annat alternativ är att infoga data i databasen med [BCP-verktyget](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Ansluta till Azure Machine Learning
Den nygenererade funktionen kan läggas till som en kolumn i en befintlig tabell eller lagras i en ny tabell och sammanfogas med den ursprungliga tabellen för maskininlärning. Funktioner kan genereras eller nås om de redan har skapats med modulen [Importera data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) i Azure ML som visas nedan:

![Azure ML-läsare](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Använda ett programmeringsspråk som Python
Att använda Python för att generera funktioner när data finns i SQL Server liknar bearbetning av data i Azure-blob med Python. En jämförelse finns i [Bearbeta Azure Blob-data i din datavetenskapsmiljö](data-blob.md). Ladda data från databasen i en pandas dataram för att bearbeta den ytterligare. Processen att ansluta till databasen och läsa in data i dataramen dokumenteras i det här avsnittet.

Följande anslutningssträngformat kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (ersätt servernamn, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas-biblioteket](https://pandas.pydata.org/) i Python tillhandahåller en omfattande uppsättning datastrukturer och dataanalysverktyg för datamanipulering för Python-programmering. Följande kod läser resultaten som returneras från en SQL Server-databas till en Pandas-dataram:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Nu kan du arbeta med Pandas-dataramen som beskrivs i ämnen [Skapa funktioner för Azure blob lagringsdata med Panda](create-features-blob.md).

