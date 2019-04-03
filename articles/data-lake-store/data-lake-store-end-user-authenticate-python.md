---
title: 'Autentisering för slutanvändare: Python med Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft Docs'
description: Lär dig att uppnå slutanvändarautentisering med Azure Data Lake Storage Gen1 med Azure Active Directory med Python
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
ms.openlocfilehash: 8b72604d7e736230911d0a0987b88d372be4ddf3
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881287"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Slutanvändarautentisering med Azure Data Lake Storage Gen1 med hjälp av Python
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API:et](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

I den här artikeln lär du dig hur du använder Python SDK för att göra slutanvändarautentisering med Azure Data Lake Storage Gen1. Slutanvändarautentisering kan ytterligare delas upp i två kategorier:

* Slutanvändarautentisering utan Multi-Factor authentication
* Slutanvändarautentisering med multifaktorautentisering

Båda dessa alternativ beskrivs i den här artikeln. Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av Python, se [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Förutsättningar

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory ”interna” program**. Du måste ha slutfört stegen i [slutanvändarautentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Installera modulerna

Att arbeta med Data Lake Storage Gen1 med hjälp av Python, måste du installera tre moduler.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* Den `azure-mgmt-datalake-store` modulen, som innehåller Azure Data Lake Storage Gen1-kontohanteringsåtgärder. Mer information om den här modulen finns i [Gen1 för Azure Data Lake lagringshantering modulreferens](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Den `azure-datalake-store` modulen, som innehåller Azure Data Lake Storage Gen1 filsystemsåtgärder. Mer information om den här modulen finns i [referensen för azure-datalake-store Filesystem-modulen](https://azure-datalake-store.readthedocs.io/en/latest/).

Installera modulerna med hjälp av följande kommandon.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

1. I valfri IDE, skapa ett nytt Python-program, till exempel **mysample.py**.

2. Lägg till följande kodavsnitt för att importera de nödvändiga modulerna

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Spara ändringarna i mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Slutanvändarautentisering med multifaktorautentisering

### <a name="for-account-management"></a>För kontohantering

Använd följande kodfragment för att autentisera med Azure AD för kontohanteringsåtgärder på Data Lake Storage Gen1-konto. Följande kodfragment kan användas för att autentisera ditt program med multifaktorautentisering. Ange värdena nedan för ett befintligt Azure AD **interna** program.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>För filsystemsåtgärder

Används för att autentisera med Azure AD för filsystemsåtgärder på Data Lake Storage Gen1-konto. Följande kodfragment kan användas för att autentisera ditt program med multifaktorautentisering. Ange värdena nedan för ett befintligt Azure AD **interna** program.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Slutanvändarautentisering utan Multi-Factor authentication

Det är inaktuellt. Mer information finns i [Azure-autentisering med hjälp av Python SDK](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder autentisering för slutanvändare för att autentisera med Azure Data Lake Storage Gen1 med hjälp av Python. Du kan nu se ut i följande artiklar som pratar om hur du använder Python för att arbeta med Azure Data Lake Storage Gen1.

* [Kontohanteringsåtgärder i Data Lake Storage Gen1 med hjälp av Python](data-lake-store-get-started-python.md)
* [Åtgärder på Data Lake Storage Gen1 med hjälp av Python](data-lake-store-data-operations-python.md)

