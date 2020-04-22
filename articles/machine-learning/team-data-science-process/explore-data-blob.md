---
title: Utforska data i Azure blob storage med pandor – Team Data Science Process
description: Så här utforskar du data som lagras i Azure blob-behållare med pandas Python-paketet.
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
ms.openlocfilehash: e429dce497411305964cb1ec5298228dc4093b1f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685959"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Utforska data i Azure blob storage med pandor

Den här artikeln beskriver hur du utforskar data som lagras i Azure blob-behållare med [pandas](https://pandas.pydata.org/) Python-paketet.

Den här uppgiften är ett steg i [teamdatavetenskapsprocessen](overview.md).

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har:

* Skapade ett Azure-lagringskonto. Om du behöver instruktioner läser du [Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md)
* Lagrade dina data i ett Azure blob storage-konto. Om du behöver instruktioner läser du [Flytta data till och från Azure Storage](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Ladda data i en pandas DataFrame
För att utforska och manipulera en datauppsättning måste den först hämtas från blob-källan till en lokal fil, som sedan kan läsas in i en pandas DataFrame. Här är stegen för att följa för den här proceduren:

1. Hämta data från Azure-blob med följande Python-kodexempel med Blob-tjänsten. Ersätt variabeln i följande kod med dina specifika värden:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

1. Läs data i en pandas DataFrame från den nedladdade filen.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Nu är du redo att utforska data och generera funktioner på den här datauppsättningen.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Exempel på datautforskning med hjälp av pandor
Här är några exempel på olika sätt att utforska data med hjälp av pandor:

1. Kontrollera **antalet rader och kolumner**

    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```

1. **Kontrollera** de första eller sista **raderna** i följande datauppsättning:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Kontrollera **datatypen** varje kolumn importerades som med hjälp av följande exempelkod

    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```

1. Kontrollera **grundstatistiken** för kolumnerna i datauppsättningen enligt följande

    ```python
    dataframe_blobdata.describe()
    ```

1. Titta på antalet poster för varje kolumnvärde enligt följande

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Räkna saknade värden** jämfört med det faktiska antalet poster i varje kolumn med hjälp av följande exempelkod

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```

1. Om du **saknar värden** för en viss kolumn i data kan du släppa dem på följande sätt:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Ett annat sätt att ersätta saknade värden är med lägesfunktionen:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Skapa ett **histogramdiagram** med variabelt antal lagerplatser för att rita fördelningen av en variabel

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Titta på **korrelationer** mellan variabler med hjälp av en scatterplot eller med hjälp av den inbyggda korrelationsfunktionen

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
