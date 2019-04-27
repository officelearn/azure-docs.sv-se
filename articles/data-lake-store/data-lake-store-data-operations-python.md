---
title: 'Python: Filsystemsåtgärder på Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Lär dig hur du använder Python SDK för att arbeta med Data Lake Storage Gen1 filsystemet.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 57efc718a51398b577a0078ba829d2f6209cab54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878831"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Filsystemsåtgärder på Azure Data Lake Storage Gen1 med hjälp av Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

I den här artikeln får du lära dig hur du använder Python SDK för att utföra filsystemsåtgärder på Azure Data Lake Storage Gen1. Anvisningar för hur du utför kontohanteringsåtgärder på Data Lake Storage Gen1 med Python finns i [Kontohanteringsåtgärder på Data Lake Storage Gen1 med hjälp av Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1 konto**. Följ anvisningarna på [Kom igång med Azure Data Lake Storage Gen1 med Azure portal](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Installera modulerna

Att arbeta med Data Lake Storage Gen1 med hjälp av Python, måste du installera tre moduler.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* Den `azure-mgmt-datalake-store` modulen, som innehåller Azure Data Lake Storage Gen1-kontohanteringsåtgärder. Mer information om den här modulen finns i den [azure-mgmt-datalake-store modulreferens](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Den `azure-datalake-store` modulen, som innehåller Azure Data Lake Storage Gen1 filsystemsåtgärder. Mer information om den här modulen finns i den [azure-datalake-store-filsystemet modulreferens](https://azure-datalake-store.readthedocs.io/en/latest/).

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

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
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

* Information om slutanvändarautentisering för programmet, se [slutanvändarautentisering med Data Lake Storage Gen1 med hjälp av Python](data-lake-store-end-user-authenticate-python.md).
* Tjänst-till-tjänst-autentisering för programmet, se [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Skapa filsystemklient

Följande kodfragment skapar först Data Lake Storage Gen1-klienten. Klientobjektet används för att skapa ett Data Lake Storage Gen1-konto. Slutligen skapar kodfragmentet ett klientobjekt för filsystemet.

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
* [Kontohanteringsåtgärder på Data Lake Storage Gen1 med hjälp av Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Se också

* [Referens för Azure Data Lake Storage Gen1 Python (filsystem)](https://azure-datalake-store.readthedocs.io/en/latest)
* [Öppna källa Big Data-program som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
