---
title: "Tjänst-till-tjänst-autentisering: Python med Data Lake Store med Azure Active Directory | Microsoft Docs"
description: "Lär dig att uppnå service to service autentisering med Data Lake Store med Azure Active Directory använder Python"
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
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: c04b870e72c5d29df95d16b96cc423441af6fd85
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-python"></a>Tjänst-till-tjänst-autentisering med Data Lake Store använder Python
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

I den här artikeln får information du om hur du använder Python SDK för att göra service to service autentisering med Azure Data Lake Store. Slutanvändarens autentisering med Data Lake Store med hjälp av Python, se [slutanvändarens autentisering med Data Lake Store med hjälp av Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Förutsättningar

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett program för Azure Active Directory ”Web”**. Du måste ha slutfört stegen i [tjänst-till-tjänst-autentisering med Data Lake Store med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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
    import logging, getpass, pprint, uuid, time
    ```

3. Spara ändringarna i mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>”Tjänst till tjänst”-autentisering med klienthemlighet för kontohantering

Använda den här fragment för att autentisera med Azure AD för kontohanteringsåtgärder på Data Lake-butiken som skapa Data Lake Store-konto, ta bort Data Lake Store-konto, osv. Följande kodavsnitt kan användas för att autentisera ditt program interaktivt, använda klienthemligheten för ett program / service principal av en befintlig Azure AD ”Webbapp” program.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Tjänst-till-tjänst-autentisering med klienthemlighet för filsystemsåtgärder

Använd följande kodavsnitt att autentisera med Azure AD för filsystemsåtgärder på Data Lake Store som skapa mappar, ladda uppladdningsfilen osv. Du kan använda följande kodfragment för att autentisera ditt program icke-interaktivt, med hjälp av klienthemligheten för ett program/tjänstobjekt. Använd detta med ett befintligt Azure AD-webbappsprogram.

    adlCreds = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE', resource = 'https://datalake.azure.net/')

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Store such as create Data Lake Store account, delete Data Lake Store account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder autentisering för tjänst-till-tjänst för att autentisera med Azure Data Lake Store med hjälp av Python. Du kan nu se följande artiklar som talar om hur du använder Python för att arbeta med Azure Data Lake Store.

* [Kontohanteringsåtgärder på Data Lake Store använder Python](data-lake-store-get-started-python.md)
* [Dataåtgärder på Data Lake Store använder Python](data-lake-store-data-operations-python.md)


