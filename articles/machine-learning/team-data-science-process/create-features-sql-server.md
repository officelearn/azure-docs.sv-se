---
title: Skapa funktioner för data i SQL Server med SQL och Python | Microsoft Docs
description: Bearbeta Data från SQL Azure
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 6729de763265355125100ccdaf0c5cd81e2bfd8d
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346558"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Skapa funktioner för data i SQL Server med SQL och Python
Det här dokumentet visar hur du skapar funktioner för data som lagras i en SQL Server-VM på Azure som hjälper algoritmer som Lär dig mer effektivt från data. Du kan använda SQL-databaser eller ett programmeringsspråk som Python för att åstadkomma detta. Båda metoderna är visas här.

Den här uppgiften är ett steg i den [Team Data Science Process (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [!NOTE]
> Ett praktiska exempel finns i [NYC Taxi datauppsättning](http://www.andresmh.com/nyctaxitrips/) och referera till IPNB benämnt [NYC Datatransformering med IPython Notebook och SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) en slutpunkt till slutpunkt genomgång.
> 
> 

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver anvisningar läser [skapa ett Azure Storage-konto](../../storage/common/storage-quickstart-create-account.md)
* Lagras dina data i SQL Server. Om du inte har Se [flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md) anvisningar om hur du flyttar data det.

## <a name="sql-featuregen"></a>Funktionen generation med SQL
I det här avsnittet beskrivs olika sätt att generera funktioner med hjälp av SQL:  

1. [Antal baserat funktionen Generation](#sql-countfeature)
2. [Datagruppering funktionen Generation](#sql-binningfeature)
3. [Lansera funktionerna från en enda kolumn](#sql-featurerollout)

> [!NOTE]
> När du har genererat ytterligare funktioner kan du lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primär nyckel, som kan kopplas till den ursprungliga tabellen.
> 
> 

### <a name="sql-countfeature"></a>Baserat på antal funktionen generation
Det här dokumentet visar två sätt att generera antal funktioner. Den första metoden använder villkorlig summan och den andra metoden använder ”where”-satsen. Dessa kan sedan kopplas till den ursprungliga tabellen (med primärnyckelskolumnerna) om du vill att antal funktioner tillsammans med den ursprungliga informationen.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Datagruppering funktionen Generation
I följande exempel visas hur du skapar binned funktioner av diskretisering (med 5 lagerplatser) en numerisk kolumn som kan användas som en funktion i stället:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Lansera funktionerna från en enda kolumn
I det här avsnittet visar vi hur du ska distribuera en enda kolumn i en tabell för att generera ytterligare funktioner. I exemplet förutsätter vi att det finns en latitud och longitud kolumn i tabellen som du vill generera funktioner.

Här är en kort introduktion på latitud/longitud platsdata (resurstilldelas från stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Här följer några användbara saker att känna till om platsdata innan du skapar funktioner från fältet:

* Inloggningssidan anger om vi är norr eller söder, östra eller Väst i världen.
* Ett annat värde än noll hundratals siffra anger longitud, latitud inte används.
* Tio siffror ger möjlighet att cirka 1 000 kilometer. Det ger användbar information om vilka kontinent eller miljöer är vi på.
* Enheter siffra (en decimal grad) ger en position upp till 111 kilometer (60 sjömil, ungefär 69 miles). Den visar ungefär, vilka stora eller det land som det är en.
* En decimal är värt att upp till 11.1 km: Det kan skilja positionen för en stor stad från en närliggande stora stad.
* Andra decimal är värt att upp till 1.1 km: Det kan skilja ett village från nästa.
* Den tredje decimalen är värt att upp till 110 m: kan identifiera ett stort agricultural fält eller institutionella campus.
* Fjärde decimal är värt att upp till 11 m: mark kan identifiera. Det är jämförbar vanliga riktighet en okorrigerad GPS-enhet utan störningar.
* Den femte decimalen är värt att upp till 1.1 m: den särskiljer träd från varandra. Precision på den här nivån med kommersiella GPS-enheter kan bara ske med differentiell korrigering.
* Den sjätte decimalen är värt att upp till 0.11 m: du kan använda detta strållayoutmotor strukturer i detalj, för att utforma landskap, för att skapa vägar. Det bör vara mer än tillräckligt bra för att spåra förflyttningar av glaciers och vattendrag. Detta kan uppnås genom att utföra painstaking åtgärder med GPS, till exempel differentially korrigerad GPS.

Platsinformationen kan vara trädmodell genom att ange region, plats och information om ort. Observera att en gång kan också anropa en REST-slutpunkt, till exempel Bing Maps-API som är tillgängliga på `https://msdn.microsoft.com/library/ff701710.aspx` att hämta information om region/distrikt.

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

Dessa funktioner i platsbaserade kan ytterligare används för att generera ytterligare antal funktioner enligt beskrivningen ovan.

> [!TIP]
> Du kan via programmering Infoga posterna språk du föredrar. Du kan behöva infogar data i segment för att förbättra effektiviteten för skrivning. [Här är ett exempel på hur du gör detta med hjälp av pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Ett annat alternativ är att infoga data i databasen med [BCP för](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Ansluta till Azure Machine Learning
Funktionen nygenererade kan läggas till som en kolumn i en befintlig tabell eller lagras i en ny tabell och anslutits med den ursprungliga tabellen för machine learning. Funktioner kan genereras eller nås om redan har skapat med hjälp av den [importdata](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modul i Azure ML enligt nedan:

![Azure ML-läsare](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Med hjälp av ett programmeringsspråk som Python
Använda Python för att skapa funktioner om data finns i SQL Server liknar bearbetning av data i Azure-blob med hjälp av Python. Jämförelse finns i [processen Azure Blob-data i miljön data science](data-blob.md). Läs in data från databasen till en pandas dataram att bearbeta det ytterligare. Processen för att ansluta till databasen och läser in data till dataramen dokumenteras i det här avsnittet.

Följande formatet för anslutningssträngen kan användas för att ansluta till en SQL Server-databas från Python med pyodbc (Ersätt servername, dbname, användarnamn och lösenord med dina specifika värden):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Den [Pandas biblioteket](http://pandas.pydata.org/) tillhandahåller en omfattande uppsättning datastrukturer och verktyg för analys av data för datamanipulering för Python-programmering i Python. Följande kod läser resultatet som returneras från en SQL Server-databas till en dataram Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Nu kan du arbeta med Pandas dataram som beskrivs i avsnitt [skapa funktioner för Azure blob storage-data med Panda](create-features-blob.md).

