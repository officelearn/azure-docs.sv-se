---
title: "Använda Python SDK för att komma igång med Azure Data Lake Store | Microsoft Docs"
description: "Lär dig hur du använder Python SDK för att arbeta med Data Lake Store-konton och filsystemet."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 8a3f3d8bfe670f2a4d1a4642b2380764aa6daeb4
ms.lasthandoff: 01/24/2017


---

# <a name="get-started-with-azure-data-lake-store-using-python"></a>Komma igång med Azure Data Lake Store med hjälp av Python

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Lär dig hur du använder Python SDK för Azure och Azure Data Lake Store för att utföra grundläggande åtgärder som att skapa mappar, överföra och hämta datafiler osv. Mer information om Data Lake finns i [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Krav

* **Python**. Du kan hämta Python [här](https://www.python.org/downloads/). I den här artikeln används Python 3.5.2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett program i Azure Active Directory**. Du kan använda Azure AD-program för att autentisera Data Lake Store-program med Azure AD. Det finns olika sätt att autentisera med Azure AD: **slutanvändarens autentisering** eller **serviceautentisering**. Instruktioner och mer information om hur du autentiserar finns i [Autentisera med Data Lake Store med Azure Active Directory (Authenticate with Data Lake Store using Azure Active Directory)](data-lake-store-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Installera modulerna

Du måste installera tre moduler för att kunna arbeta med Data Lake Store med hjälp av Python.

* `azure-mgmt-resource`-modulen. Den här modulen innehåller Azure-moduler för Active Directory osv.
* `azure-mgmt-datalake-store`-modulen. Den här modulen innehåller Azure Data Lake Store-kontohanteringsåtgärder. Mer information om den här modulen finns i [referensen för Azure Data Lake Store Management-modulen](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* `azure-datalake-store`-modulen. Den här modulen innehåller Azure Data Lake Store-filsystemsåtgärder. Mer information om den här modulen finns i [referensen för Azure Data Lake Store Filesystem-modulen](http://azure-datalake-store.readthedocs.io/en/latest/).

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

* Slutanvändarautentisering
* Tjänst-till-tjänst-autentisering
* Multi-Factor Authentication

Du måste använda dessa autentiseringsalternativ för både kontohanterings- och filsystemhanteringsmoduler.

### <a name="end-user-authentication-for-account-management"></a>Slutanvändarautentisering för kontohantering

Använd den här metoden om du vill autentisera med Azure AD för kontohanteringsåtgärder (skapa/ta bort Data Lake Store-konto osv.). Du måste ange användarnamn och lösenord för en Azure AD-användare. Observera att användaren inte bör konfigureras för multifaktorautentisering.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="end-user-authentication-for-filesystem-operations"></a>Slutanvändarautentisering för filsystemsåtgärder

Använd den här metoden om du vill autentisera med Azure AD för filsystemsåtgärder (skapa mapp, överför fil osv.). Använd den här metoden med ett befintligt **inbyggt Azure AD-klientprogram**. Den Azure AD-användare som du anger autentiseringsuppgifter för bör inte konfigureras för multifaktorautentisering.

    tenant_id = 'FILL-IN-HERE'
    client_id = 'FILL-IN-HERE'
    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    token = lib.auth(tenant_id, user, password, client_id)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>”Tjänst till tjänst”-autentisering med klienthemlighet för kontohantering

Använd den här metoden om du vill autentisera med Azure AD för kontohanteringsåtgärder (skapa/ta bort Data Lake Store-konto osv.). Du kan använda följande kodfragment för att autentisera ditt program icke-interaktivt, med hjälp av klienthemligheten för ett program/tjänstobjekt. Använd detta med ett befintligt Azure AD-webbappsprogram.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

### <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Tjänst-till-tjänst-autentisering med klienthemlighet för filsystemsåtgärder

Använd den här metoden om du vill autentisera med Azure AD för filsystemsåtgärder (skapa mapp, överför fil osv.). Du kan använda följande kodfragment för att autentisera ditt program icke-interaktivt, med hjälp av klienthemligheten för ett program/tjänstobjekt. Använd detta med ett befintligt Azure AD-webbappsprogram.

    token = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE')

### <a name="multi-factor-authentication-for-account-management"></a>Multifaktorautentisering för kontohantering

Använd den här metoden om du vill autentisera med Azure AD för kontohanteringsåtgärder (skapa/ta bort Data Lake Store-konto osv.). Följande kodfragment kan användas för att autentisera ditt program med multifaktorautentisering. Använd detta med ett befintligt Azure AD-webbappsprogram.

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
    credentials = AADTokenCredentials(mgmt_token, client_id)

### <a name="multi-factor-authentication-for-filesystem-management"></a>Multifaktorautentisering för hantering av filsystem

Använd den här metoden om du vill autentisera med Azure AD för filsystemsåtgärder (skapa mapp, överför fil osv.). Följande kodfragment kan användas för att autentisera ditt program med multifaktorautentisering. Använd detta med ett befintligt Azure AD-webbappsprogram.

    token = lib.auth(tenant_id='FILL-IN-HERE')

## <a name="create-an-azure-resource-group"></a>Skapa en Azure-resursgrupp

Använd följande kodfragment för att skapa en Azure-resursgrupp:

    ## Declare variables
    subscriptionId= 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'
    
    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )
    
    ## Create an Azure Resource Group
    resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )

## <a name="create-clients-and-data-lake-store-account"></a>Skapa klienter och Data Lake Store-konto

Följande kodfragment skapar först Data Lake Store-klienten. Klientobjektet används för att skapa ett Data Lake Store-konto. Slutligen skapar kodfragmentet ett klientobjekt för filsystemet.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(credentials, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(token, store_name=adlsAccountName)

## <a name="list-the-data-lake-store-accounts"></a>Visa en lista med Data Lake Store-konton

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

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

## <a name="see-also"></a>Se även

- [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
- [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Store .NET SDK-referens](https://msdn.microsoft.com/library/mt581387.aspx)
- [Data Lake Store REST-referens](https://msdn.microsoft.com/library/mt693424.aspx)

