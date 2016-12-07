---
title: "Komma igång med Azure Data Lake Analytics med hjälp av Python | Azure Docs"
description: "Lär dig hur du använder Python för att skapa ett Data Lake Store-konto och skicka jobb. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: e94b11ab62afbea9381772a2bdb668fdc514d609
ms.openlocfilehash: 2da23d881a23cc0fc23b63a7b9e06d9af9a755eb


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>Självstudier: Komma igång med Azure Data Lake Analytics med hjälp av Python
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lär dig hur du använder Python för att skapa Azure Data Lake Analytics-konton, definiera Data Lake Analytics-jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-konton. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

I de här självstudierna skapar du ett jobb som läser en fil med tabbavgränsade värden (TSV) och konverterar den till en fil med kommaavgränsade värden (CSV). Klicka på flikarna överst i det här avsnittet om du vill gå igenom samma självstudier med andra verktyg.

##<a name="prerequisites"></a>Krav

Innan du börjar den här självstudiekursen behöver du följande:

- **en Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Ett Azure Active Directory-program**. Du kan använda Azure AD-program för att autentisera Data Lake Store-program med Azure AD. Du kan autentisera med Azure AD på olika sätt: med slutanvändarautentisering eller ”tjänst till tjänst”-autentisering. Instruktioner och mer information om hur du autentiserar finns i [Autentisera med Data Lake Store med Azure Active Directory (Authenticate with Data Lake Store using Azure Active Directory)](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).
- **[Python](https://www.python.org/downloads/)**. Du måste använda 64-bitarsversionen. I den här artikeln används Python version 3.5.2.


## <a name="install-azure-python-sdk"></a>Installera Azure Python SDK

Du måste installera tre moduler för att kunna arbeta med Data Lake Store med hjälp av Python.

Azure-modulen innehåller Azure Data Lake Store-kontohanteringsmoduler, förutom andra Azure-moduler för Active Directory osv. Modulen azure-datalake-store omfattar Azure Data Lake Store-filsystemåtgärder. Modulen azure-datalake-analytics omfattar Azure Data Lake Analytics-åtgärderna. Installera modulerna med hjälp av följande kommandon.

    pip install azure
    pip install azure-datalake-store
    pip install azure-datalake-analytics

## <a name="create-a-python-application"></a>Skapa ett Python-program

1. Använd valfri IDE för att skapa ett nytt Python-program, till exempel mysample.py.

2. Lägg till följande rader för att importera de nödvändiga modulerna

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store.account import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.account.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeAnalyticsAccountProperties, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. Spara ändringarna i Python-programfilen.

## <a name="authentication"></a>Autentisering

Använd någon av följande metoder för att autentisera:

### <a name="end-user-authentication-for-account-management"></a>Slutanvändarautentisering för kontohantering

Använd den här metoden om du vill autentisera med Azure AD för kontohanteringsåtgärder (skapa/ta bort Data Lake Store-konto osv.). Du måste ange användarnamn och lösenord för en Azure AD-användare. Användarkontot kan inte konfigureras för multifaktorautentisering och kontot kan inte vara ett Microsoft-konto/Live ID, till exempel @outlook.com, eller @live.com.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>”Tjänst till tjänst”-autentisering med klienthemlighet för kontohantering

Använd den här metoden om du vill autentisera med Azure AD för kontohanteringsåtgärder (skapa/ta bort Data Lake Store-konto osv.). Du kan använda följande kodfragment för att autentisera ditt program icke-interaktivt, med hjälp av klienthemligheten för ett program/tjänstobjekt. Använd detta med ett befintligt Azure AD-webbappsprogram.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>Skapa resurshanteringsgrupp

Använd följande kodfragment för att skapa en Azure-resursgrupp:

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>Skapa ett Data Lake Store-konto

Varje Data Lake Analytics-konto kräver ett Data Lake Store-konto. Anvisningar finns i [Skapa ett Data Lake Store-konto](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).



## <a name="create-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>Skicka Data Lake Analytics-jobb

Data Lake Analytics-jobb skrivs på U-SQL-språket. Läs mer om U-SQL i [Kom igång med U-SQL-språket](data-lake-analytics-u-sql-get-started.md) och [Referens för U-SQL-språket](http://go.microsoft.com/fwlink/?LinkId=691348).

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>Nästa steg

- Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
- Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Information om att utveckla U-SQL-program finns i [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).
- Om du vill få en översikt över Data Lake Analytics, se [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).




<!--HONumber=Nov16_HO3-->


