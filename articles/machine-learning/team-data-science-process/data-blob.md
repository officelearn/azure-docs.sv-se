---
title: Bearbeta Azure blob-data med avancerade analyser | Microsoft Docs
description: Bearbeta Data i Azure Blob storage.
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d8a59078-91d3-4440-b85c-430363c3f4d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: garye;bradsev
ms.openlocfilehash: f3388728b2c2ea699a2caf764dc0fd3e9ff19505
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="heading"></a>Bearbeta Azure blob-data med avancerade analyser
Det här dokumentet beskriver utforska data och genererar funktioner från data som lagras i Azure Blob storage. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Läsa in data i ett Pandas data
För att utforska och ändra en datamängd, måste den hämtas från blob-källan till en lokal fil som sedan kan läsas in i en Pandas data ram. Här följer de steg för den här proceduren:

1. Hämta data från Azure blob med följande Python exempelkod med blob-tjänsten. Ersätt variabeln i koden nedan med dina specifika värden: 
   
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
2. Läsa data i en Pandas data-ram från den hämta filen.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Du är nu redo att utforska data och generera funktioner på denna dataset.

## <a name="blob-dataexploration"></a>Datagranskning
Här följer några exempel på hur du kan utforska data med hjälp av Pandas:

1. Kontrollera antalet rader och kolumner 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Granska de första eller sista raderna i datauppsättningen enligt nedan:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Kontrollera den datatyp som varje kolumn har importerats som använder följande exempelkod
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Kontrollera följande grundläggande statistik för kolumner i datauppsättning
   
        dataframe_blobdata.describe()
5. Titta på hur många poster för varje värde i kolumnen enligt följande
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Antal saknade värden jämfört med det faktiska antalet poster i varje kolumn med hjälp av följande exempelkod
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Om du har värden som saknas för en viss kolumn i data, kan du släppa dem på följande sätt:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna()   dataframe_blobdata_noNA.shape
   
   Ett annat sätt att ersätta saknade värden är med funktionen läge:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Skapa ett histogram ritytans med hjälp av variabeln antal lagerplatser ska ritas distribution av en variabel    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Titta på korrelationer mellan variabler med hjälp av en scatterplot eller inbyggda korrelationen
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Funktionen Generation
Vi kan generera funktioner med Python på följande sätt:

### <a name="blob-countfeature"></a>Indikatorvärdet baserat funktionen Generation
Kategoriska funktioner kan skapas på följande sätt:

1. Kontrollera distributionen av kolumnen kategoriska:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Generera indikator värden för varje kolumnvärdena
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Ansluta till kolumnen indikator med den ursprungliga dataramen 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Ta bort själva ursprungliga variabeln:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Diskretisering funktionen Generation
För att generera binned funktioner fortsätta enligt följande:

1. Lägga till en sekvens av kolumner till bin en numerisk kolumn
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Konvertera diskretisering till en sekvens med booleska variabler
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Slutligen ansluta variablerna dummy tillbaka till den ursprungliga dataramen
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Data skrivs tillbaka till Azure blob och använda i Azure Machine Learning
När du har gjort data och skapa nödvändiga funktioner, kan du överföra data (provtagning eller featurized) till ett Azure blob- och använda den i Azure Machine Learning med följande steg: Observera att du kan skapa ytterligare funktioner i Azure Machine Learning Studio samt. 

1. Skriva dataramen till en lokal fil
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Överför data till Azure blob på följande sätt:
   
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
3. Nu går att läsa data från blob med hjälp av Azure Machine Learning [importera Data] [ import-data] modulen som visas på skärmen nedan:

![läsaren blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

