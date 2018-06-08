---
title: Utforska data i Azure blob storage med Pandas | Microsoft Docs
description: Så här utforska data som lagras i Azure blob-behållaren med hjälp av Pandas.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: feaa9e54-01e0-48c8-a917-1eba0f9d9ec7
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: c727df985f3285f5def9bfdc249ee27b4d748a01
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837056"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Utforska data i Azure Blob Storage med Pandas
Det här dokumentet innehåller information om hur att utforska data som lagras i Azure blob-behållaren med [Pandas](http://pandas.pydata.org/) Python-paketet.

Följande **menyn** länkar till avsnitt som beskriver hur du använder Verktyg för att utforska data från olika miljöer för lagring. Den här uppgiften är ett steg i den [datavetenskap Process]().

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver mer information, se [skapa ett Azure Storage-konto](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Lagra data i ett Azure blob storage-konto. Om du behöver mer information, se [flytta data till och från Azure Storage](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Läsa in data i en Pandas DataFrame
Om du vill utforska och ändra en datamängd, måste den först hämtas från blob-källan till en lokal fil som sedan kan läsas in i en Pandas DataFrame. Här följer de steg för den här proceduren:

1. Hämta data från Azure blob med följande Python-kodexempel med blob-tjänsten. Ersätt variabeln i följande kod med din specifika värden: 
   
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

## <a name="blob-dataexploration"></a>Exempel på undersökning av data med hjälp av Pandas
Här följer några exempel på hur du kan utforska data med hjälp av Pandas:

1. Granska de **antal rader och kolumner** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Inspektera** de första eller sista få **rader** i följande datauppsättningen:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Kontrollera den **datatyp** varje kolumn har importerats som använder följande exempelkod
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Kontrollera den **grundläggande statistik** för kolumnerna i följande data
   
        dataframe_blobdata.describe()
5. Titta på hur många poster för varje värde i kolumnen enligt följande
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Antal saknade värden** jämfört med det faktiska antalet poster i varje kolumn med hjälp av följande exempelkod
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Om du har **saknade värden** för en viss kolumn i data, kan du släppa dem på följande sätt:
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna() dataframe_blobdata_noNA.shape
   
   Ett annat sätt att ersätta saknade värden är med funktionen läge:
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna ({< kolumnnamn >: .mode()[0]}) dataframe_blobdata ['< kolumnnamn >']        
8. Skapa en **histogram** ritas med hjälp av variabeln antal lagerplatser ska ritas distribution av en variabel    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Titta på **korrelationer** mellan variabler med hjälp av en scatterplot eller inbyggda korrelationen
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

