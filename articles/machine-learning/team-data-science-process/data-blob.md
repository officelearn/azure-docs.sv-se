---
title: Bearbeta Azure-blob-data med avancerad analys – Team Data Science Process
description: Utforska data och generera funktioner från data som lagras i Azure Blob-lagring med hjälp av avancerad analys.
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
ms.openlocfilehash: 4c47dfb8b221b6cb4b6237669ecd17c1637107a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721106"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Bearbeta Azure-blobbdata med avancerade analyser
Det här dokumentet omfattar att utforska data och generera funktioner från data som lagras i Azure Blob-lagring. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Ladda data i en Pandas-dataram
För att utforska och manipulera en datauppsättning måste den hämtas från blob-källan till en lokal fil som sedan kan läsas in i en Pandas-dataram. Här är stegen för att följa för den här proceduren:

1. Hämta data från Azure-blob med följande exempel på Python-kod med Blob-tjänsten. Ersätt variabeln i koden nedan med dina specifika värden: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. Läs data i en Pandas-dataram från den nedladdade filen.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Nu är du redo att utforska data och generera funktioner på den här datauppsättningen.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Data Exploration
Här är några exempel på olika sätt att utforska data med hjälp av Pandor:

1. Kontrollera antalet rader och kolumner 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Kontrollera de första eller sista raderna i datauppsättningen enligt nedan:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Kontrollera datatypen varje kolumn importerades som med hjälp av följande exempelkod
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Kontrollera grundstatistiken för kolumnerna i datauppsättningen enligt följande
   
        dataframe_blobdata.describe()
5. Titta på antalet poster för varje kolumnvärde enligt följande
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Räkna saknade värden jämfört med det faktiska antalet poster i varje kolumn med hjälp av följande exempelkod
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Om du saknar värden för en viss kolumn i data kan du släppa dem på följande sätt:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   Ett annat sätt att ersätta saknade värden är med lägesfunktionen:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Skapa ett histogramdiagram med variabelt antal lagerplatser för att rita fördelningen av en variabel    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Titta på korrelationer mellan variabler med hjälp av en scatterplot eller med hjälp av den inbyggda korrelationsfunktionen
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Generering av funktioner
Vi kan generera funktioner med Python enligt följande:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Värdebaserad funktionsgenerering för indikator
Kategoriska funktioner kan skapas på följande sätt:

1. Kontrollera fördelningen av den kategoriska kolumnen:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Generera indikatorvärden för vart och ett av kolumnvärdena
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Sammanfoga indikatorkolumnen med den ursprungliga dataramen 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Ta bort själva originalvariabeln:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Generering av binningsfunktion
För att generera binned funktioner, gör vi följande:

1. Lägga till en sekvens av kolumner på en numerisk kolumn
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Konvertera binning till en sekvens av booleska variabler
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Slutligen ansluter du tillbaka dummyvariablerna till den ursprungliga dataramen
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Skriva tillbaka data till Azure-blob och konsumera i Azure Machine Learning
När du har utforskat data och skapat de nödvändiga funktionerna kan du överföra data (samplat eller featurized) till en Azure-blob och använda dem i Azure Machine Learning med hjälp av följande steg: Ytterligare funktioner kan skapas i Azure Machine Learning Studio (klassisk) också. 

1. Skriva dataramen till lokal fil
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Ladda upp data till Azure-blob enligt följande:
   
        from azure.storage.blob import BlobService
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
3. Nu kan data läsas från blobben med hjälp av modulen Azure Machine Learning [Import Data][import-data] som visas på skärmen nedan:

![läsare blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

