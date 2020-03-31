---
title: 'Python: Kontohanteringsåtgärder på Azure Data Lake Storage Gen1 | Microsoft-dokument'
description: Lär dig hur du använder Python SDK för att arbeta med Azure Data Lake Storage Gen1-kontohanteringsåtgärder.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c71136ecb57fac460514b5f4815ba19cc22d86cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76290622"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Kontohanteringsåtgärder på Azure Data Lake Storage Gen1 med Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Lär dig hur du använder Python SDK för Azure Data Lake Storage Gen1 för att utföra grundläggande kontohanteringsåtgärder som att skapa ett Data Lake Storage Gen1-konto, lista DataSjölagringsgenm1-konton osv. Instruktioner om hur du utför filsystemåtgärder på Data Lake Storage Gen1 med Python finns i [Filesystem-åtgärder på Data Lake Storage Gen1 med Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Krav

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **En Azure-resursgrupp**. Instruktioner finns i [skapa en Azure-resursgrupp](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Installera modulerna

Om du vill arbeta med Data Lake Storage Gen1 med Python måste du installera tre moduler.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* Modulen, `azure-mgmt-datalake-store` som innehåller Azure Data Lake Storage Gen1-kontohanteringsåtgärder. Mer information om den här modulen finns i [Azure Data Lake Storage Gen1 Management-modulens referens](/python/api/azure-mgmt-datalake-store/).
* Modulen, `azure-datalake-store` som innehåller azure data lake storage gen1-filsystemåtgärder. Mer information om den här modulen finns i [filsystemmodulreferens för azure-datalake-store.For](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/)more information on this module, see azure-datalake-store filesystem module reference .

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

## <a name="authentication"></a>Autentisering

I det här avsnittet tittar vi på hur du kan autentisera med Azure AD på olika sätt. De tillgängliga alternativen är:

* För slutanvändareautentisering för ditt program, se [Slutanvändarens autentisering med Data Lake Storage Gen1 med Python](data-lake-store-end-user-authenticate-python.md).
* För tjänst-till-tjänst-autentisering för ditt program finns i Autentisering från [Tjänst till tjänst med Data Lake Storage Gen1 med Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Skapa klient- och datalagringsgenm1-konto

Följande kodavsnitt skapar först klienten för Data Lake Storage Gen1-konto. Den använder klientobjektet för att skapa ett Data Lake Storage Gen1-konto. Slutligen skapar kodfragmentet ett klientobjekt för filsystemet.

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

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Lista kontona för datasjölagring gen1

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Ta bort datasjölagringsgenm1-kontot

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Nästa steg
* [Filsystemåtgärder på Data Lake Storage Gen1 med Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Se även

* [Azure-datalake-store Python (Filesystem) referens](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Big Data-program med öppen källkod som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
