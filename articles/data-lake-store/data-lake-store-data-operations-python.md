---
title: "Python: Filsystemsåtgärder på Azure Data Lake Store | Microsoft Docs"
description: "Lär dig hur du använder Python SDK för att arbeta med Data Lake Store-filsystemet."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 3540e58a58f20842979212ba41f11ce2908941f5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-rest-api"></a>Filsystemsåtgärder på Azure Data Lake Store med hjälp av REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

I den här artikeln får du lära dig hur du använder Python SDK till att utföra filsystemsåtgärder på Azure Data Lake Store. Instruktioner för hur du utför kontohanteringsåtgärder på Data Lake Store med Python finns i [Kontohanteringsåtgärder på Data Lake Store med hjälp av Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Krav

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Store-konto**. Följ instruktionerna i [Kom igång med Azure Data Lake Store med hjälp av Azure Portal](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Installera modulerna

Du måste installera tre moduler för att kunna arbeta med Data Lake Store med hjälp av Python.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* `azure-mgmt-datalake-store`-modulen, som innehåller Azure Data Lake Store-kontohanteringsåtgärder. Mer information om den här modulen finns i [referensen för Azure Data Lake Store Management-modulen](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* `azure-datalake-store`-modulen, som innehåller Azure Data Lake Store-filsystemsåtgärder. Mer information om den här modulen finns i [referensen för Azure Data Lake Store Filesystem-modulen](http://azure-datalake-store.readthedocs.io/en/latest/).

Installera modulerna med hjälp av följande kommandon.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

1. Skapa ett nytt Python-program i valfritt IDE, t.ex. **mysample.py**.

2. Lägg till följande rader för att importera de nödvändiga modulerna

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Spara ändringarna i mysample.py.

## <a name="authentication"></a>Autentisering

I det här avsnittet tittar vi på hur du kan autentisera med Azure AD på olika sätt. De tillgängliga alternativen är:

* Information om slutanvändarautentisering för programmet finns i [Slutanvändarautentisering med Data Lake Store med hjälp av Python](data-lake-store-end-user-authenticate-python.md).
* Information om tjänst-till-tjänst-autentisering för programmet finns i [Tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Skapa filsystemklient

Följande kodfragment skapar först Data Lake Store-klienten. Klientobjektet används för att skapa ett Data Lake Store-konto. Slutligen skapar kodfragmentet ett klientobjekt för filsystemet.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Skapa en katalog

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Överför en fil


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Hämta en fil

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Ta bort en katalog

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Nästa steg
* [Kontohanteringsåtgärder på Azure Data Lake Store med hjälp av Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Se även
* [Referens för Azure Data Lake Store Python (kontohantering)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Referens för Azure Data Lake Store Python (filsystem)](http://azure-datalake-store.readthedocs.io/en/latest)
* [Stordataprogram med öppen källkod som är kompatibla med Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)
