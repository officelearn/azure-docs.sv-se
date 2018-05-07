---
title: Skapa funktioner för Azure blob storage-data med hjälp av Panda | Microsoft Docs
description: Så här skapar du funktioner för data som lagras i Azure blob-behållaren med Panda Python-paketet.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 7d105e8e8cf67cd28c0be8a02b26913a25eed023
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>Skapa funktioner för Azure Blob Storage-data med Panda
Det här dokumentet beskrivs hur du skapar funktioner för data som lagras i Azure blob-behållaren med den [Pandas](http://pandas.pydata.org/) Python-paketet. Efter beskriver hur du läser in data i ett Panda data visas hur du skapar kategoriska funktioner med hjälp av Python-skript med indikatorn värden och diskretisering funktioner.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Detta **menyn** länkar till avsnitt som beskriver hur du skapar funktioner för data i olika miljöer. Den här uppgiften är ett steg i den [Team Data vetenskap processen (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har skapat ett Azure blob storage-konto och har sparat dina data. Om du behöver anvisningar för att konfigurera ett konto, se [skapa ett Azure Storage-konto](../../storage/common/storage-create-storage-account.md#create-a-storage-account)

## <a name="load-the-data-into-a-pandas-data-frame"></a>Läsa in data i ett Pandas data
För att utforska och ändra en datamängd hämtas den från käll-blob till en lokal fil. Läsa in den i ett Pandas data. Här följer de steg för den här proceduren:

1. Hämta data från Azure blob med följande Python exempelkod med blob-tjänsten. Ersätt variabeln i följande kod med din specifika värden:
   
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

## <a name="blob-featuregen"></a>Funktionen Generation
I följande två avsnitt visar hur du skapar kategoriska funktioner med indikatorn värden och diskretisering funktioner med hjälp av Python-skript.

### <a name="blob-countfeature"></a>Indikatorns värde baserat funktionen Generation
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
Göra så här för att generera binned funktioner:

1. Lägga till en sekvens av kolumner till bin en numerisk kolumn
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Konvertera diskretisering till en sekvens med booleska variabler
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Slutligen ansluta variablerna dummy tillbaka till den ursprungliga dataramen
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="sql-featuregen"></a>Data skrivs tillbaka till Azure blob att använda i Azure Machine Learning
Att nyttja data i Azure Machine Learning som du har gjort provtagning eller featurized, ladda upp data till en Azure blob. Ytterligare funktioner kan skapas i Azure Machine Learning Studio samt. Följande steg visar hur du överför data:

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
3. Nu går att läsa data från blob med hjälp av Azure Machine Learning [importera Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modulen som visas i följande skärmbild visar:

![läsaren blob](./media/data-blob/reader_blob.png)

