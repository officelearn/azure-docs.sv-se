---
title: Skapa funktioner för data i SQL Server med SQL och Python | Microsoft Docs
description: Bearbetning av Data från SQL Azure
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: ''
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 206ae59ff84699eb8419d068d2f2102759ac82c1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Skapa funktioner för data i SQL Server med SQL och Python
Det här dokumentet beskrivs hur du skapar funktioner för data som lagras i en SQL Server-VM på Azure som hjälper algoritmer Lär dig mer effektivt från data. Du kan använda SQL- eller programmeringsspråk som Python för att utföra den här uppgiften. Båda metoderna visas här.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Detta **menyn** länkar till avsnitt som beskriver hur du skapar funktioner för data i olika miljöer. Den här uppgiften är ett steg i den [Team Data vetenskap processen (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [!NOTE]
> Ett praktiskt exempel, finns det [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) och referera till IPNB med rubriken [NYC Data wrangling IPython anteckningsboken och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) för en slutpunkt till slutpunkt-hanteringspaketen.
> 
> 

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver mer information, se [skapa ett Azure Storage-konto](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Lagrade data i SQL Server. Om du inte har Se [flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md) anvisningar om hur du flyttar data det.

## <a name="sql-featuregen"></a>Funktionen generation med SQL
I det här avsnittet beskrivs olika sätt att skapa funktioner med hjälp av SQL:  

1. [Antal baserat funktionen Generation](#sql-countfeature)
2. [Diskretisering funktionen Generation](#sql-binningfeature)
3. [Lansera funktionerna från en enda kolumn](#sql-featurerollout)

> [!NOTE]
> När du skapar ytterligare funktioner, kan du lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primärnyckel som kan kopplas till den ursprungliga tabellen.
> 
> 

### <a name="sql-countfeature"></a>Antal baserat funktionen generation
Det här dokumentet visar två sätt att generera antal funktioner. Den första metoden använder Villkorlig summering och den andra metoden använder uttrycket 'where'. Dessa kan sedan kopplas till den ursprungliga tabellen (med primärnyckelkolumnerna) om du vill att antalet funktioner tillsammans med den ursprungliga informationen.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Diskretisering funktionen Generation
I följande exempel visas hur du skapar binned funktioner av diskretisering (med 5 lagerplatser) en numerisk kolumn som kan användas som en funktion i stället:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Lansera funktionerna från en enda kolumn
I det här avsnittet visar vi hur du lanserar en kolumn i en tabell för att generera ytterligare funktioner. Exemplet förutsätter att det finns en latitud och longitud kolumn i tabellen som du vill generera funktioner.

Här är en kort introduktion om latitud/longitud platsdata (resurstilldelas från stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Här följer några användbara saker att förstå om lokaliseringsuppgifter innan du skapar funktioner från fältet:

* Inloggningssidan anger om vi är norr eller söder, Öst eller Väst på jorden.
* Ett annat värde än noll hundratals siffran indikerar longitud, latitud inte används.
* Flera siffra ger möjlighet att ca 1 000 kilometer. Den ger användbar information om vilka kontinent eller oceanen vi finns på.
* Enheter siffra (en decimal grad) ger en position upp till 111 kilometer (60 sjömil, om 69 miles). Det anger ungefär, vilka stora eller det land som vi finns i.
* En decimal är värd upp till 11.1 km: den kan skilja positionen för en stor ort från en närliggande stora stad.
* Till andra decimaltecknet som är värd upp till 1.1 km: den kan skilja en village från nästa.
* Den tredje decimaltecknet är högst 110 m: den kan identifiera en stor jordbruket institutionella plats.
* Den fjärde decimaltecknet är upp till 11 m: mark kan identifiera. Det är jämförbar med vanliga precision på en GPS-enhet som inte har korrigerats utan störningar.
* Femte decimaltecknet är upp till 1.1 m: den särskiljer träd från varandra. Precision på den här nivån med kommersiella GPS-enheter kan endast ske med differentiell korrigering.
* Sjätte decimaltecknet är värt upp till 0.11 m: kan du använda den för att utforma strukturer i detalj för att utforma landskap, skapa vägar. Det bör vara mer än tillräckligt bra för spårning av glaciers och floder. Detta kan uppnås genom att göra painstaking åtgärder med GPS, till exempel differentially korrigerade GPS.

Platsinformationen kan vara featurized genom att avgränsa region, plats och information om ort. Observera att en gång kan även anropa en REST-slutpunkt, till exempel Bing Maps API på `https://msdn.microsoft.com/library/ff701710.aspx` att hämta information om region/distrikt.

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

Funktionerna plats baserat kan ytterligare användas för att generera ytterligare antal funktioner som tidigare beskrivits.

> [!TIP]
> Du kan via programmering Infoga posterna med ditt språk. Du kan behöva infoga data i segment att förbättra effektiviteten för skrivning. [Här är ett exempel på hur du gör detta med hjälp av pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Ett annat alternativ är att infoga data i en databas med hjälp av [BCP-verktyget](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Ansluta till Azure Machine Learning
Funktionen nyligen skapade kan läggas till som en kolumn i en befintlig tabell eller lagras i en ny tabell och kopplas till den ursprungliga tabellen för machine learning. Funktioner kan genereras eller komma åt om redan har skapats med hjälp av den [importera Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modul i Azure ML enligt nedan:

![Azure ML-läsare](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Med hjälp av programmeringsspråk som Python
Använder Python för att generera funktioner när data är i SQL Server liknar bearbetning av data i Azure blob använder Python. Jämförelse finns [processen Azure Blob-data i datavetenskap miljön](data-blob.md). Läsa in data från databasen i ett pandas data att bearbeta det ytterligare. Processen för att ansluta till databasen och läsa in data i data-ram dokumenteras i det här avsnittet.

Följande connection-strängformat kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (Ersätt servername, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Den [Pandas biblioteket](http://pandas.pydata.org/) i Python ger en omfattande uppsättning datastrukturer och verktyg för analys av data till datamanipulering för Python-programmering. Följande kod läser resultatet som returneras från en SQL Server-databas till en Pandas data ram:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Nu kan du arbeta med Pandas dataramen som beskrivs i avsnitt [skapa funktioner för Azure blob storage-data med hjälp av Panda](create-features-blob.md).

