---
title: "Exempeldata i SQLServer på Azure | Microsoft Docs"
description: "Exempeldata i SQLServer på Azure"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 33c030d4-5cca-4cc9-99d7-2bd13a3926af
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: fashah;garye;bradsev
ms.openlocfilehash: fbd83ad59a9db1daca4ba16402031e2c1c5b7991
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="heading"></a>Exempeldata i SQL Server på Azure
Det här dokumentet visar hur du samla in data som lagras i SQL Server på Azure med hjälp av SQL- eller Python-programmeringsspråket. Den visar även hur du flyttar samplade data till Azure Machine Learning genom att spara den till en fil, överföra den till en Azure blob och läsa den i Azure Machine Learning Studio.

Python provtagning använder den [pyodbc](https://code.google.com/p/pyodbc/) ODBC-biblioteket för att ansluta till SQL Server på Azure och [Pandas](http://pandas.pydata.org/) biblioteket för att göra beräkningarna.

> [!NOTE]
> Exempelkoden SQL i det här dokumentet förutsätter att data i en SQL Server på Azure. Om det inte finns [flytta data till SQL Server på Azure](move-sql-server-virtual-machine.md) avsnittet för instruktioner om hur du flyttar dina data till SQL Server på Azure.
> 
> 

Följande **menyn** länkar till avsnitt som beskriver hur du exempeldata från olika miljöer för lagring. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Varför exempel dina data?**
Om datamängden som du planerar att analysera är stort, men det är vanligtvis en bra idé att ned-sample data för att minska det till en mindre men representativt och mer användarvänlig storlek. Detta underlättar data förstå undersökning och funktionen tekniker. Roll i den [Team Data vetenskap processen (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) är att aktivera snabb prototyper för databearbetning funktions- och maskininlärning modeller.

Sampling uppgiften är ett steg i den [Team Data vetenskap processen (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="SQL"></a>Med SQL
Det här avsnittet beskrivs flera metoder som använder SQL för att utföra enkla slumpmässig provtagning mot data i databasen. Välj en metod baserat på datastorleken på din och dess distributionsplatser.

De två objekten nedan visar hur du använder newid i SQL Server för att utföra beräkningarna. Vilken metod du väljer beror på hur slumpmässiga du vill att samplet som ska vara (pk_id i koden nedan antas vara en automatiskt genererad primärnyckel).

1. Mindre strikta slumpmässigt prov
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Fler slumpmässigt prov 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample kan vara används för provtagning som visas nedan. Detta kan vara en bättre metod om dina data är stor (förutsatt att data på olika sidor inte korreleras) och för frågan ska slutföras inom en rimlig tid.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Du kan utforska och generera funktioner från den här samplade data genom att lagra det i en ny tabell
> 
> 

### <a name="sql-aml"></a>Ansluta till Azure Machine Learning
Du kan använda exempelfrågor ovan direkt i Azure Machine Learning [importera Data] [ import-data] modulen ned-sample data direkt och sätta den i ett Azure Machine Learning-experiment. En skärmbild av med hjälp av modulen läsare för att läsa samplade data visas nedan:

![läsaren sql][1]

## <a name="python"></a>Använder Python programmeringsspråket
Det här avsnittet visas hur du använder den [pyodbc biblioteket](https://code.google.com/p/pyodbc/) att upprätta en ODBC ansluta till en SQL server-databas i Python. Anslutningssträngen för databasen är följande: (Ersätt servername, dbname, användarnamn och lösenord med din konfiguration):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Den [Pandas](http://pandas.pydata.org/) i Python-bibliotek innehåller ett stort utbud av datastrukturer och verktyg för analys av data för datamanipulering för Python-programmering. Koden nedan läser en 0,1% exempeldata från en tabell i Azure SQL-databas i en Pandas data:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Nu kan du arbeta med samplade data i ramen Pandas data. 

### <a name="python-aml"></a>Ansluta till Azure Machine Learning
Du kan använda följande exempelkod för att spara provtagning ned data till en fil och överför den till en Azure blob. Data i blob direkt kan läsas in i ett Experiment i Azure Machine Learning med hjälp av den [importera Data] [ import-data] modul. Stegen är följande: 

1. Skriva ramen pandas data till en lokal fil
   
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
3. Läsa data från Azure blob med hjälp av Azure Machine Learning [importera Data] [ import-data] modulen som visas i den skärmen grab nedan:

![läsaren blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Team vetenskap av data i åtgärden exempel
En slutpunkt till slutpunkt genomgången exempel på Team av vetenskapliga data med en offentlig dataset finns [Team vetenskap av data i praktiken: med hjälp av SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
