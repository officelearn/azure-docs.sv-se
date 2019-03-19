---
title: Exempeldata i SQLServer på Azure - Team Data Science Process
description: Exempeldata lagras i SQL Server på Azure med SQL- eller Python-programmeringsspråket och flytta den till Azure Machine Learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a544ddb6f31481750b1cd46b52d2909d71739707
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897086"
---
# <a name="heading"></a>Exempeldata i SQL Server på Azure

Den här artikeln visar hur du exempel på data som lagras i SQL Server på Azure med SQL- eller Python-programmeringsspråket. Den visar också hur du flyttar samplade data till Azure Machine Learning genom att spara den till en fil, överföra den till en Azure-blob och läser den i Azure Machine Learning Studio.

Python sampling använder den [pyodbc](https://code.google.com/p/pyodbc/) ODBC-biblioteket att ansluta till SQL Server på Azure och [Pandas](https://pandas.pydata.org/) bibliotek som ska göra är samplingen.

> [!NOTE]
> SQL-exempelkoden i det här dokumentet förutsätter att data är i en SQL Server på Azure. Om den inte avser [flytta data till SQL Server på Azure](move-sql-server-virtual-machine.md) artikeln anvisningar om hur du flyttar dina data till SQL Server på Azure.
> 
> 

**Varför prov på dina data?**
Om datauppsättningen som du planerar att analysera är stor, men det är oftast en bra idé att nedåtsampla data för att minska det till en mindre men representativa och mer hanterbara storlek. Detta underlättar förståelse av data, utforskning och funktioner. Dess roll i den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) är att snabbt skapa prototyper för bearbetning av funktions- och machine learning-modeller.

Den här aktiviteten för sampling är ett steg i den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="SQL"></a>Med hjälp av SQL
Det här avsnittet beskrivs flera metoder som använder SQL för att utföra enkla stickprov mot data i databasen. Välj en metod baserat på datastorleken på din och motsvarande distribution.

Följande två objekt visar hur du använder `newid` i SQL Server för att utföra är samplingen. Vilken metod du väljer beror på hur slumpmässiga du vill att exemplet ska vara (pk_id i följande exempelkod antas vara en automatiskt genererad primärnyckel).

1. Mindre strikta slumpmässigt urval
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Mer slumpmässigt urval 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample kan användas för sampling data samt. Detta kan vara en bättre metod om dina data är stora (förutsatt att data på olika sidor inte korreleras) och för frågan ska slutföras inom rimlig tid.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Du kan utforska och skapa funktioner från den här samplade data genom att lagra det i en ny tabell
> 
> 

### <a name="sql-aml"></a>Ansluta till Azure Machine Learning
Du kan använda exempelfrågor ovan direkt i Azure Machine Learning [importdata] [ import-data] modulen att nedåtsampla data direkt och ta med dem i ett Azure Machine Learning-experiment. En skärmbild av med läsmodulen för att läsa samplade data visas här:

![läsare sql][1]

## <a name="python"></a>Med hjälp av Python programmeringsspråket
Det här avsnittet visar hur du använder den [pyodbc biblioteket](https://code.google.com/p/pyodbc/) att upprätta en ODBC ansluta till en SQL server-databas i Python. Databasens anslutningssträng är följande: (Ersätt servername, dbname, användarnamn och lösenord med din konfiguration):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Den [Pandas](https://pandas.pydata.org/) i Python-bibliotek innehåller ett stort utbud av datastrukturer och verktyg för analys av data för datamanipulering för Python-programmering. Följande kod läser in en 0,1% exempeldata från en tabell i Azure SQL-databas i en Pandas-data:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Du kan nu arbeta med exempeldata i dataram Pandas. 

### <a name="python-aml"></a>Ansluta till Azure Machine Learning
Du kan använda följande exempelkod för att spara data samplas ned till en fil och överföra den till en Azure-blob. Data i blob kan läsas direkt till en Azure Machine Learning-Experiment med hjälp av den [importdata] [ import-data] modulen. Stegen är följande: 

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
3. Läsa data från Azure-blob med hjälp av Azure Machine Learning [importdata] [ import-data] modulen enligt följande skärm fälten:

![läsare blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Team Data Science Process i åtgärden exemplet
För att gå igenom ett exempel på Team Data Science Process med en offentlig datauppsättning, se [Team Data Science Process i praktiken: med SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
