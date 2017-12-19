---
title: "Slutanvändarens autentisering: Python med Data Lake Store med Azure Active Directory | Microsoft Docs"
description: "Lär dig att uppnå slutanvändarens autentisering med Data Lake Store med Azure Active Directory med Python"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 48990c57fb10127733623000a105507b5a48d900
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-python"></a>Slutanvändarens autentisering med Data Lake Store använder Python
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

I den här artikeln får information du om hur du använder Python SDK för att göra slutanvändarens autentisering med Azure Data Lake Store. Slutanvändarens autentisering kan ytterligare delas upp i två kategorier:

* Slutanvändarens autentisering utan Multi-Factor authentication
* Slutanvändarens autentisering med Multi-Factor authentication

Båda dessa alternativ beskrivs i den här artikeln. Tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av Python, se [tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Krav

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory ”interna” program**. Du måste ha slutfört stegen i [slutanvändarens autentisering med Data Lake Store med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

1. I IDE du väljer att skapa ett nytt Python-program, till exempel **mysample.py**.

2. Lägg till följande kodavsnitt för att importera de nödvändiga modulerna

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Spara ändringarna i mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Slutanvändarens autentisering med Multi-Factor authentication

### <a name="for-account-management"></a>För kontohantering

Använd följande fragment för att autentisera med Azure AD för kontohanteringsåtgärder på ett Data Lake Store-konto. Följande kodfragment kan användas för att autentisera ditt program med multifaktorautentisering. Ange värden under för en befintlig Azure AD **interna** program.

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

Används för att autentisera med Azure AD för filsystemsåtgärder på ett Data Lake Store-konto. Följande kodfragment kan användas för att autentisera ditt program med multifaktorautentisering. Ange värden under för en befintlig Azure AD **interna** program.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Slutanvändarens autentisering utan Multi-Factor authentication

Detta är föråldrad. Mer information finns i [Azure-autentisering med hjälp av Python SDK](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder slutanvändarens autentisering för att autentisera med Azure Data Lake Store med hjälp av Python. Du kan nu se följande artiklar som talar om hur du använder Python för att arbeta med Azure Data Lake Store.

* [Kontohanteringsåtgärder på Data Lake Store använder Python](data-lake-store-get-started-python.md)
* [Dataåtgärder på Data Lake Store använder Python](data-lake-store-data-operations-python.md)

