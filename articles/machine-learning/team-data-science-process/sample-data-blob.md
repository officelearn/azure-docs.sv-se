---
title: Exempeldata i Azure blob storage | Microsoft Docs
description: Exempeldata i Azure Blob Storage
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 7dbd5f932f2d090a3f9fa8f917d12869a94dc553
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847090"
---
# <a name="heading"></a>Exempeldata i Azure blob-lagring

Den här artikeln beskriver sampling av data lagras i Azure blob storage genom att hämta den via programmering och sampling den med hjälp av procedurerna som skrivits i Python.

**Varför prov på dina data?**
Om datauppsättningen som du planerar att analysera är stor, men det är oftast en bra idé att nedåtsampla data för att minska det till en mindre men representativa och mer hanterbara storlek. Detta underlättar förståelse av data, utforskning och funktioner. Dess roll i Cortana Analytics-processen är att snabbt skapa prototyper för bearbetning av funktions- och machine learning-modeller.

Den här aktiviteten för sampling är ett steg i den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="download-and-down-sample-data"></a>Ladda ned och nedåtsampla data
1. Hämta data från Azure blob storage med hjälp av blobtjänsten från Python-exempelkoden följande: 
   
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

2. Läsa data i en Pandas-dataram från den fil som hämtats ovan.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Nedåtsampla data med den `numpy`'s `random.choice` på följande sätt:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Nu kan du arbeta med ovanstående dataram med exemplet för ytterligare utforskning och funktionen generation 1 procent.

## <a name="heading"></a>Överföra data och läsa in den i Azure Machine Learning
Du kan använda följande exempelkod för att nedåtsampla data och använda den direkt i Azure Machine Learning:

1. Skriva dataramen till en lokal fil
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Ladda upp den lokala filen till en Azure-blob med hjälp av följande exempelkod:
   
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

3. Läsa data från Azure-blob med hjälp av Azure Machine Learning [importdata](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) enligt bilden nedan:

![läsare blob](./media/sample-data-blob/reader_blob.png)

