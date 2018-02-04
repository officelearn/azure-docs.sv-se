---
title: Exempeldata i Azure blob storage | Microsoft Docs
description: Exempeldata i Azure Blob Storage
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e8d9ad2c-86c5-43d6-80b8-d355b5c0dccf
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: garye;bradsev
ms.openlocfilehash: 1e5cf5da727b6720c936e43147beb8d87fb925cd
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="heading"></a>Exempeldata i Azure blob storage
Det här dokumentet beskriver provtagning data som lagras i Azure blob storage genom att hämta den programmässigt och provtagning den med hjälp av procedurerna som skrivits i Python.

Följande **menyn** länkar till avsnitt som beskriver hur du exempeldata från olika miljöer för lagring. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Varför exempel dina data?**
Om datamängden som du planerar att analysera är stort, men det är vanligtvis en bra idé att ned-sample data för att minska det till en mindre men representativt och mer användarvänlig storlek. Detta underlättar data förstå undersökning och funktionen tekniker. Roll i Cortana Analytics processen är att aktivera snabb prototyper för databearbetning funktions- och maskininlärning modeller.

Sampling uppgiften är ett steg i den [Team Data vetenskap processen (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="download-and-down-sample-data"></a>Hämta och ned exempeldata
1. Hämta data från Azure blob storage med hjälp av blob-tjänsten från följande Python exempelkod: 
   
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

2. Läsa data i en Pandas data-ram från filen hämtade ovan.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Ned-sample data med hjälp av den `numpy`'s `random.choice` på följande sätt:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Nu kan du arbeta med ovan dataramen med 1 procent-exempel för vidare undersökning och funktionen generation.

## <a name="heading"></a>Överföra data och läsa in den i Azure Machine Learning
Du kan använda följande exempelkod ned-sample data och använda den direkt i Azure Machine Learning:

1. Skriva dataramen till en lokal fil
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Överför den lokala filen till en Azure blob med hjälp av följande exempelkod:
   
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

3. Läsa data från Azure-blobben med hjälp av Azure Machine Learning [importera Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) som visas i bilden nedan:

![läsaren blob](./media/sample-data-blob/reader_blob.png)

