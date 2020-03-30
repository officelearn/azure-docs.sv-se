---
title: 'Autentisering för slutanvändare: Python med Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du uppnår slutanvändarautentisering med Azure Data Lake Storage Gen1 med Azure Active Directory med Python
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
ms.openlocfilehash: c69f6c1f587285c5c52280c4c49008764d5b20d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265602"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Slutanvändarens autentisering med Azure Data Lake Storage Gen1 med Python
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

I den här artikeln får du lära dig mer om hur du använder Python SDK för att göra slutanvändarens autentisering med Azure Data Lake Storage Gen1. Autentisering av slutanvändare kan delas in i två kategorier:

* Autentisering av slutanvändare utan multifaktorautentisering
* Autentisering för slutanvändare med multifaktorautentisering

Båda dessa alternativ diskuteras i den här artikeln. För tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Python finns i [Autentisering från Tjänst till tjänst med Data Lake Storage Gen1 med Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Krav

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett "native"-program i Azure Active Directory**. Du måste ha slutfört stegen i [Slutanvändarautentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Installera modulerna

Om du vill arbeta med Data Lake Storage Gen1 med Python måste du installera tre moduler.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* Modulen, `azure-mgmt-datalake-store` som innehåller Azure Data Lake Storage Gen1-kontohanteringsåtgärder. Mer information om den här modulen finns i [Azure Data Lake Storage Gen1 Management-modulens referens](/python/api/azure-mgmt-datalake-store/).
* Modulen, `azure-datalake-store` som innehåller azure data lake storage gen1-filsystemåtgärder. Mer information om den här modulen finns i [referens för filsystemmodulreferens för azure-datalake-store-store filsystemmodul](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Installera modulerna med hjälp av följande kommandon.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

1. I den IDE som du väljer skapar du ett nytt Python-program, till exempel **mysample.py**.

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

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Autentisering för slutanvändare med multifaktorautentisering

### <a name="for-account-management"></a>För kontohantering

Använd följande kodavsnitt för att autentisera med Azure AD för kontohanteringsåtgärder på ett Data Lake Storage Gen1-konto. Följande kodfragment kan användas för att autentisera ditt program med multifaktorautentisering. Ange värdena nedan för ett befintligt Azure **AD-inbyggt** program.

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

### <a name="for-filesystem-operations"></a>För filsystemåtgärder

Använd detta för att autentisera med Azure AD för filsystemåtgärder på ett Data Lake Storage Gen1-konto. Följande kodfragment kan användas för att autentisera ditt program med multifaktorautentisering. Ange värdena nedan för ett befintligt Azure **AD-inbyggt** program.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Autentisering av slutanvändare utan multifaktorautentisering

Detta är inaktuellt. Mer information finns i [Azure Authentication med Python SDK](/azure/python/python-sdk-azure-authenticate).
   
## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig hur du använder slutanvändarens autentisering för att autentisera med Azure Data Lake Storage Gen1 med Python. Du kan nu titta på följande artiklar som beskriver hur du använder Python för att arbeta med Azure Data Lake Storage Gen1.

* [Kontohanteringsåtgärder på Data Lake Storage Gen1 med Python](data-lake-store-get-started-python.md)
* [Dataåtgärder på DataSjölagring Gen1 med Python](data-lake-store-data-operations-python.md)

