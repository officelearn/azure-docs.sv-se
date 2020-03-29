---
title: 'Python: Filsystemåtgärder på Azure Data Lake Storage Gen1 | Microsoft-dokument'
description: Lär dig hur du använder Python SDK för att arbeta med filsystemet Data Lake Storage Gen1.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 17b53c508f0c4c5ba8fa257fcdc692cdaa45d470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294226"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Filsystemåtgärder på Azure Data Lake Storage Gen1 med Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

I den här artikeln får du lära dig hur du använder Python SDK för att utföra filsystemåtgärder på Azure Data Lake Storage Gen1. Instruktioner om hur du utför kontohanteringsåtgärder på Data Lake Storage Gen1 med Python finns i [Kontohanteringsåtgärder på Data Lake Storage Gen1 med Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Krav

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1-konto**. Följ instruktionerna på [Kom igång med Azure Data Lake Storage Gen1 med Hjälp av Azure-portalen](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Installera modulerna

Om du vill arbeta med Data Lake Storage Gen1 med Python måste du installera tre moduler.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* Modulen, `azure-mgmt-datalake-store` som innehåller Azure Data Lake Storage Gen1-kontohanteringsåtgärder. Mer information om den här modulen finns i [referensen för azure-mgmt-datalake-store-modul](/python/api/azure-mgmt-datalake-store/).
* Modulen, `azure-datalake-store` som innehåller azure data lake storage gen1-filsystemåtgärder. Mer information om den här modulen finns i [filmodulreferensen för filsystemmodul för azure-datalake-store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

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

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Spara ändringarna i mysample.py.

## <a name="authentication"></a>Autentisering

I det här avsnittet tittar vi på hur du kan autentisera med Azure AD på olika sätt. De tillgängliga alternativen är:

* För slutanvändareautentisering för ditt program, se [Slutanvändarens autentisering med Data Lake Storage Gen1 med Python](data-lake-store-end-user-authenticate-python.md).
* För tjänst-till-tjänst-autentisering för ditt program finns i Autentisering från [Tjänst till tjänst med Data Lake Storage Gen1 med Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Skapa filsystemklient

Följande kodavsnitt skapar först klienten för Data Lake Storage Gen1-konto. Den använder klientobjektet för att skapa ett Data Lake Storage Gen1-konto. Slutligen skapar kodfragmentet ett klientobjekt för filsystemet.

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
* [Kontohanteringsåtgärder på Data Lake Storage Gen1 med Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Se även

* [Azure Data Lake Storage Gen1 Python (Filesystem) Referens](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Big Data-program med öppen källkod som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
