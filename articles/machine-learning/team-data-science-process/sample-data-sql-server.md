---
title: Exempeldata i SQL Server på Azure - Team Data Science Process
description: Exempeldata som lagras i SQL Server på Azure med SQL- eller Python-programmeringsspråket och flyttar dem sedan till Azure Machine Learning.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717655"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Exempeldata i SQL Server på Azure

Den här artikeln visar hur du tar exempel på data som lagras i SQL Server på Azure med antingen SQL- eller Python-programmeringsspråket. Den visar också hur du flyttar samplade data till Azure Machine Learning genom att spara den i en fil, ladda upp den till en Azure-blob och sedan läsa den i Azure Machine Learning Studio.

Python-samplingen använder [pyodbc](https://code.google.com/p/pyodbc/) ODBC-biblioteket för att ansluta till SQL Server på Azure och [Pandas-biblioteket](https://pandas.pydata.org/) för att utföra samplingen.

> [!NOTE]
> Exempel på SQL-kod i det här dokumentet förutsätter att data finns i en SQL Server på Azure. Om så inte är fallet läser du [Flytta data till SQL Server på Azure-artikeln](move-sql-server-virtual-machine.md) för instruktioner om hur du flyttar dina data till SQL Server på Azure.
> 
> 

**Varför ta prov på dina data?**
Om datauppsättningen som du planerar att analysera är stor är det oftast en bra idé att nedexpför att ta bort data för att minska dem till en mindre men representativ och mer hanterbar storlek. Sampling underlättar data förståelse, utforskning och funktionsteknik. Dess roll i [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) är att möjliggöra snabb prototyper av databehandlingsfunktioner och maskininlärningsmodeller.

Den här samplingsuppgiften är ett steg i [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="using-sql"></a><a name="SQL"></a>Använda SQL
I det här avsnittet beskrivs flera metoder med SQL för att utföra enkel slumpmässig sampling mot data i databasen. Välj en metod baserat på din datastorlek och dess fördelning.

Följande två objekt visar `newid` hur du använder i SQL Server för att utföra samplingen. Vilken metod du väljer beror på hur slumpmässigt du vill att exemplet ska vara (pk_id i följande exempelkod antas vara en autogenererad primärnyckel).

1. Mindre strikt slumpmässigt urval
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Mer slumpmässigt urval 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tabellsamman kan också användas för att ta prover på data. Det här alternativet kan vara en bättre metod om datastorleken är stor (förutsatt att data på olika sidor inte är korrelerade) och för att frågan ska slutföras inom rimlig tid.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Du kan utforska och generera funktioner från de här exempeldata genom att lagra dem i en ny tabell
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Ansluta till Azure Machine Learning
Du kan direkt använda exempelfrågorna ovan i modulen Azure Machine Learning [Import Data][import-data] för att göra ett duperg att ta bort data i farten och föra in dem i ett Azure Machine Learning-experiment. En skärmdump av att använda läsarmodulen för att läsa de samplade data visas här:

![läsare sql][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Använda programmeringsspråket Python
Det här avsnittet visas med hjälp av [pyodbc-biblioteket](https://code.google.com/p/pyodbc/) för att upprätta en ODBC-anslutning till en SQL-serverdatabas i Python. Databasanslutningssträngen är följande: (ersätt servernamn, dbname, användarnamn och lösenord med din konfiguration):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas-biblioteket](https://pandas.pydata.org/) i Python tillhandahåller en omfattande uppsättning datastrukturer och dataanalysverktyg för datamanipulering för Python-programmering. Följande kod läser ett 0,1 % exempel på data från en tabell i Azure SQL Database till pandadata:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Du kan nu arbeta med de utvalda data i Pandas dataram. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Ansluta till Azure Machine Learning
Du kan använda följande exempelkod för att spara nedsturmplade data i en fil och överföra den till en Azure-blob. Data i blobben kan läsas direkt i ett Azure Machine Learning-experiment med modulen [Importera data.][import-data] Stegen är följande: 

1. Skriv pandas-dataramen till en lokal fil
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Ladda upp lokal fil till Azure-blob
   
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
3. Läs data från Azure blob med hjälp av Azure Machine Learning [Import Data][import-data] module som visas i följande skärm hugg:

![läsare blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Exempel på teamdatavetenskapsprocess i åtgärd
Information om hur du går igenom ett exempel på Team Data Science Process a med hjälp av en offentlig datauppsättning finns [i Team Data Science Process in Action: använda SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
