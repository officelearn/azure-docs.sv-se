---
title: 'Python: Kontohanteringsåtgärder på Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Lär dig hur du använder Python SDK för att arbeta med Azure Data Lake Storage Gen1 kontohanteringsåtgärder.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 06c341da73ad8df96d5c97e1cb9da4b7fd5c804e
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820662"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Kontohanteringsåtgärder på Azure Data Lake Storage Gen1 med hjälp av Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Lär dig hur du använder Python SDK för Azure Data Lake Storage Gen1 för att utföra grundläggande hanteringsåtgärder som att skapa ett Data Lake Storage Gen1-konto, lista över Data Lake Storage Gen1 konton osv. Anvisningar att utföra filsystemsåtgärder på Data Lake Storage Gen1 med Python finns i [filsystemsåtgärder på Data Lake Storage Gen1 med hjälp av Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Förutsättningar

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **En Azure-resursgrupp**. Instruktioner finns i [skapa en Azure-resursgrupp](../azure-resource-manager/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Installera modulerna

Att arbeta med Data Lake Storage Gen1 med hjälp av Python, måste du installera tre moduler.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* Den `azure-mgmt-datalake-store` modulen, som innehåller Azure Data Lake Storage Gen1-kontohanteringsåtgärder. Mer information om den här modulen finns i [Gen1 för Azure Data Lake lagringshantering modulreferens](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Den `azure-datalake-store` modulen, som innehåller Azure Data Lake Storage Gen1 filsystemsåtgärder. Mer information om den här modulen finns i [referensen för azure-datalake-store filesystem-modulen](http://azure-datalake-store.readthedocs.io/en/latest/).

Installera modulerna med hjälp av följande kommandon.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

1. Skapa ett nytt Python-program i valfritt IDE, t.ex. **mysample.py**.

2. Lägg till följande kodavsnitt för att importera de nödvändiga modulerna

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Spara ändringarna i mysample.py.

## <a name="authentication"></a>Authentication

I det här avsnittet tittar vi på hur du kan autentisera med Azure AD på olika sätt. De tillgängliga alternativen är:

* Information om slutanvändarautentisering för programmet, se [slutanvändarautentisering med Data Lake Storage Gen1 med hjälp av Python](data-lake-store-end-user-authenticate-python.md).
* Tjänst-till-tjänst-autentisering för programmet, se [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Skapa klienten och Gen1 för Data Lake Storage-konto

Följande kodfragment skapar först Data Lake Storage Gen1-klienten. Klientobjektet används för att skapa ett Data Lake Storage Gen1-konto. Slutligen skapar kodfragmentet ett klientobjekt för filsystemet.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create Data Lake Storage Gen1 account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Storage Gen1 account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Lista över Gen1 för Data Lake Storage-konton

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Ta bort Data Lake Storage Gen1-konto

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Nästa steg
* [Filsystemsåtgärder på Data Lake Storage Gen1 med hjälp av Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Se också

* [azure-datalake-store Python (Filesystem) reference](http://azure-datalake-store.readthedocs.io/en/latest)
* [Öppna källa Big Data-program som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
