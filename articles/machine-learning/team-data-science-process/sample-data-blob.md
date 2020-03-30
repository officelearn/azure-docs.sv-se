---
title: Exempel på data i Azure blob storage - Team Data Science Process
description: Samplingsdata som lagras i Azure blob storage genom att hämta den programmässigt och sedan sampling det med hjälp av procedurer skrivna i Python.
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
ms.openlocfilehash: 4832762a88073f4d819925659bf9078e18f60c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720290"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Exempeldata i Azure Blob Storage

Den här artikeln omfattar samplingsdata som lagras i Azure blob storage genom att hämta den programmässigt och sedan sampling det med hjälp av procedurer skrivna i Python.

**Varför ta prov på dina data?**
Om datauppsättningen som du planerar att analysera är stor är det oftast en bra idé att nedexpför att ta bort data för att minska dem till en mindre men representativ och mer hanterbar storlek. Sampling underlättar data förståelse, utforskning och funktionsteknik. Dess roll i Cortana Analytics Process är att möjliggöra snabb prototyper av databehandlingsfunktioner och maskininlärningsmodeller.

Den här samplingsuppgiften är ett steg i [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="download-and-down-sample-data"></a>Ladda ned och ned-exempel data
1. Hämta data från Azure blob storage med Blob-tjänsten från följande exempel Python-kod: 
   
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

2. Läs data i en Pandas-dataram från filen som hämtats ovan.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Ned-prov data med `numpy`hjälp `random.choice` av 's enligt följande:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Nu kan du arbeta med ovanstående dataram med exemplet Procent för ytterligare utforskning och funktionsgenerering.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Ladda upp data och läsa dem i Azure Machine Learning
Du kan använda följande exempelkod för att nedextmpla data och använda dem direkt i Azure Machine Learning:

1. Skriva dataramen till en lokal fil
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Ladda upp den lokala filen till en Azure-blob med följande exempelkod:
   
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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Läs data från Azure blob med hjälp av Azure Machine Learning [Import Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) som visas i bilden nedan:

![läsare blob](./media/sample-data-blob/reader_blob.png)

