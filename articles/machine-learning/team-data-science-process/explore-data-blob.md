---
title: Utforska data i Azure blob storage med pandas | Microsoft Docs
description: Så här att utforska data som lagras i Azure blob-behållare med pandas.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 12e277168d27786581d14adc4c32b94d0979df6d
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441596"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Utforska data i Azure blob storage med pandas

Den här artikeln beskriver hur du utforska data som lagras i Azure blob-behållaren med [pandas](http://pandas.pydata.org/) Python-paketet.

Den här uppgiften är ett steg i den [Team Data Science Process](overview.md).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver anvisningar läser [skapa ett Azure Storage-konto](../../storage/common/storage-quickstart-create-account.md)
* Lagras dina data i ett Azure blob storage-konto. Om du behöver anvisningar läser [flytta data till och från Azure Storage](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Läsa in data i en pandas-DataFrame
För att utforska och ändra en datauppsättning, måste den först ned från blob-källan till en lokal fil som sedan kan läsas in i en pandas-DataFrame till. Här följer stegen för den här proceduren:

1. Hämta data från Azure-blob med följande Python-kodexempel som använder blob-tjänsten. Ersätt variabeln i följande kod med din specifika värden: 
   
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
2. Läsa in datan i en pandas-DataFrame från den hämta filen.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Nu är du redo att utforska data och generera funktioner för den här datauppsättningen.

## <a name="blob-dataexploration"></a>Exempel på datagranskning med pandas
Här följer några exempel på hur du kan utforska data med hjälp av pandas:

1. Granska den **antal rader och kolumner** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Inspektera** de första eller sista få **rader** i följande datauppsättningen:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Kontrollera den **datatypen** varje kolumn har importerats som med hjälp av följande exempelkod
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Kontrollera den **grundläggande statistik** för kolumnerna i följande data
   
        dataframe_blobdata.describe()
5. Titta på antalet poster för varje kolumnvärde enligt följande
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Räkna värden som saknas** jämfört med det faktiska antalet poster i varje kolumn med hjälp av följande exempelkod
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Om du har **saknade värden** för en viss kolumn i data, kan du släppa dem på följande sätt:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape
   
   Ett annat sätt att ersätta värden som saknas är med funktionen läge:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna ({< kolumnnamn >: dataframe_blobdata ['< kolumnnamn >'] .mode()[0]})        
8. Skapa en **histogram** rita med hjälp av variabeln antal lagerplatser ska ritas distributionen av en variabel    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Titta på **korrelationer** mellan variabler med hjälp av en spridningsdiagrammet eller inbyggda korrelationen
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

