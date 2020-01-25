---
title: Exempeldata i SQLServer på Azure - Team Data Science Process
description: Exempeldata lagras i SQL Server på Azure med SQL- eller Python-programmeringsspråket och flytta den till Azure Machine Learning.
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
ms.openlocfilehash: 71a2ec9dc4d644fb8739db3817e2cd1d09913da7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717655"
---
# <a name="heading"></a>Exempeldata i SQL Server på Azure

Den här artikeln visar hur du exempel på data som lagras i SQL Server på Azure med SQL- eller Python-programmeringsspråket. Den visar också hur du flyttar samplade data till Azure Machine Learning genom att spara den till en fil, överföra den till en Azure-blob och läser den i Azure Machine Learning Studio.

Python sampling använder den [pyodbc](https://code.google.com/p/pyodbc/) ODBC-biblioteket att ansluta till SQL Server på Azure och [Pandas](https://pandas.pydata.org/) bibliotek som ska göra är samplingen.

> [!NOTE]
> SQL-exempelkoden i det här dokumentet förutsätter att data är i en SQL Server på Azure. Om den inte avser [flytta data till SQL Server på Azure](move-sql-server-virtual-machine.md) artikeln anvisningar om hur du flyttar dina data till SQL Server på Azure.
> 
> 

**Varför prov på dina data?**
Om datauppsättningen som du planerar att analysera är stor, men det är oftast en bra idé att nedåtsampla data för att minska det till en mindre men representativa och mer hanterbara storlek. Sampling underlättar data förståelse, utforskning och funktions teknik. Dess roll i den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) är att snabbt skapa prototyper för bearbetning av funktions- och machine learning-modeller.

Den här aktiviteten för sampling är ett steg i den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="SQL"></a>Med hjälp av SQL
Det här avsnittet beskrivs flera metoder som använder SQL för att utföra enkla stickprov mot data i databasen. Välj en metod baserat på datastorleken på din och motsvarande distribution.

Följande två objekt visar hur du använder `newid` i SQL Server för att utföra är samplingen. Vilken metod du väljer beror på hur slumpmässiga du vill att exemplet ska vara (pk_id i följande exempel kod antas vara en automatiskt genererad primär nyckel).

1. Mindre strikta slumpmässigt urval
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Mer slumpmässigt urval 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample kan användas för sampling data samt. Det här alternativet kan vara ett bättre tillvägagångs sätt om din data storlek är stor (förutsatt att data på olika sidor inte är korrelerade) och att frågan slutförs inom rimlig tid.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Du kan utforska och skapa funktioner från den här samplade data genom att lagra det i en ny tabell
> 
> 

### <a name="sql-aml"></a>Ansluta till Azure Machine Learning
Du kan direkt använda exempel frågorna ovan i modulen Azure Machine Learning [Importera data][import-data] för att nedåt – sampla data i farten och ta dem i ett Azure Machine Learning experiment. En skärm bild av hur du använder modulen läsare för att läsa exempel data visas här:

![läsare sql][1]

## <a name="python"></a>Med hjälp av Python programmeringsspråket
Det här avsnittet visar hur du använder den [pyodbc biblioteket](https://code.google.com/p/pyodbc/) att upprätta en ODBC ansluta till en SQL server-databas i Python. Databas anslutnings strängen ser ut så här: (Ersätt servername, dbname, username och Password med din konfiguration):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Den [Pandas](https://pandas.pydata.org/) i Python-bibliotek innehåller ett stort utbud av datastrukturer och verktyg för analys av data för datamanipulering för Python-programmering. Följande kod läser ett 0,1% exempel av data från en tabell i Azure SQL Database till en Pandas-data:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Du kan nu arbeta med exempeldata i dataram Pandas. 

### <a name="python-aml"></a>Ansluta till Azure Machine Learning
Du kan använda följande exempelkod för att spara data samplas ned till en fil och överföra den till en Azure-blob. Data i blobben kan läsas direkt i ett Azure Machine Learning experiment med hjälp av modulen [Importera data][import-data] . Stegen är följande: 

1. Skriva dataram pandas till en lokal fil
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Ladda upp en lokal fil till Azure-blob
   
        from azure.storage import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Läs data från Azure Blob med Azure Machine Learning [Importera][import-data] datamodul som visas på följande skärm bild:

![läsare blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Team Data Science Process i åtgärden exemplet
Om du vill gå igenom ett exempel på en grupp data vetenskaps process a med en offentlig data uppsättning, se [team data science process i praktiken: använda SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
