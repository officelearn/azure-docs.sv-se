---
title: 'Tjänst-till-tjänst-autentisering: Python med Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft Docs'
description: Lär dig att uppnå tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med Azure Active Directory med hjälp av Python
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
ms.openlocfilehash: 84b7fac10374c1c8f23d17ad775d522b4cb261e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877762"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med hjälp av Python
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

I den här artikeln lär du dig hur du använder Python SDK för att göra tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1. Slutanvändarautentisering med Data Lake Storage Gen1 med hjälp av Python, se [slutanvändarautentisering med Data Lake Storage Gen1 med hjälp av Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Nödvändiga komponenter

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.6.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett program för Azure Active Directory ”Web”**. Du måste ha slutfört stegen i [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Installera modulerna

Att arbeta med Data Lake Storage Gen1 med hjälp av Python, måste du installera tre moduler.

* `azure-mgmt-resource`-modulen, som innehåller Azure-moduler för Active Directory osv.
* Den `azure-mgmt-datalake-store` modulen, som innehåller kontohanteringsåtgärder för Data Lake Storage Gen1. Mer information om den här modulen finns i [Gen1 för Azure Data Lake lagringshantering modulreferens](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Den `azure-datalake-store` modulen, som innehåller Data Lake Storage Gen1 filsystemsåtgärder. Mer information om den här modulen finns i [referensen för azure-datalake-store Filesystem-modulen](https://azure-datalake-store.readthedocs.io/en/latest/).

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

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
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

Använd det här kodfragmentet för att autentisera med Azure AD för kontohanteringsåtgärder i Data Lake Storage Gen1 som att skapa ett Data Lake Storage Gen1, ta bort ett Data Lake Storage Gen1 konto, osv. Följande kodavsnitt kan användas för att autentisera ditt program icke-interaktivt, med hjälp av klienthemligheten för ett program / tjänstobjekt för ett befintligt Azure AD ”Webbapp” program.

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

Använd följande kodfragment för att autentisera med Azure AD för filsystemsåtgärder på Data Lake Storage Gen1 till exempel skapa mapp, Överför fil osv. Du kan använda följande kodfragment för att autentisera ditt program icke-interaktivt, med hjälp av klienthemligheten för ett program/tjänstobjekt. Använd detta med ett befintligt Azure AD-webbappsprogram.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

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
I den här artikeln har du lärt dig hur du använder tjänst-till-tjänst-autentisering för att autentisera med Data Lake Storage Gen1 med hjälp av Python. Du kan nu se ut i följande artiklar som pratar om hur du använder Python för att arbeta med Data Lake Storage Gen1.

* [Kontohanteringsåtgärder i Data Lake Storage Gen1 med hjälp av Python](data-lake-store-get-started-python.md)
* [Åtgärder på Data Lake Storage Gen1 med hjälp av Python](data-lake-store-data-operations-python.md)


