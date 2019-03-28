---
title: Hantera Azure Data Lake Analytics med hjälp av Python
description: Den här artikeln beskriver hur du använder Python för att hantera Data Lake Analytics-konton, datakällor, användare och jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 82007c780a0c9ff3bb2e1a50a4826499f9df9c9f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518974"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Hantera Azure Data Lake Analytics med hjälp av Python
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Den här artikeln beskriver hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av Python.

## <a name="supported-python-versions"></a>Python-versioner som stöds

* Använda en 64-bitars version av Python.
* Du kan använda standard Python-distribution finns på  **[Python.org hämtar](https://www.python.org/downloads/)**. 
* Många utvecklare tycker att det är praktiskt att använda den  **[Anaconda Python-distribution](https://www.anaconda.com/download/)**.  
* Den här artikeln skrevs med hjälp av Python version 3.6 från standard Python-distribution

## <a name="install-azure-python-sdk"></a>Installera Azure Python SDK

Installera följande moduler:

* Den **azure-mgmt-resource** modulen innehåller andra Azure-moduler för Active Directory osv.
* Den **azure-datalake-store** modulen innehåller Azure Data Lake Store-filsystemsåtgärder. 
* Den **azure-mgmt-datalake-store** modulen innehåller Azure Data Lake Store-kontohanteringsåtgärder.
* Den **azure-mgmt-datalake-analytics** modulen innehåller Azure Data Lake Analytics-åtgärder. 

Kontrollera först att du har senast `pip` genom att köra följande kommando:

```
python -m pip install --upgrade pip
```

Det här dokumentet skrevs med `pip version 9.0.1`.

Använd följande `pip` kommandon för att installera modulerna från kommandoraden:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Skapa ett nytt pythonskript

Klistra in följande kod i skriptet:

```python
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

## Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

## Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

## Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

## Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

Kör skriptet för att kontrollera att modulerna som kan importeras.

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interaktiv användarautentisering med ett popup-fönster

Den här metoden stöds inte.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktiv användarautentisering med en kod för enhet

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Icke-interaktiv autentisering med SPI och en hemlighet

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Icke-interaktiv autentisering med API: et och ett certifikat

Den här metoden stöds inte.

## <a name="common-script-variables"></a>Vanliga skriptvariabler

Dessa variabler används i exemplen.

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Skapa klienter

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Skapa en Azure-resursgrupp

```python
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
```

## <a name="create-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto

Skapa först ett store-konto.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Skapa sedan ett ADLA-konto som använder det arkivet.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Skicka ett jobb

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Vänta tills ett jobb för att avsluta

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Lista över pipelines och upprepningar
Beroende på om dina jobb har pipelinen eller en återkommande metadata som är ansluten kan visa du pipelines och upprepningar.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Hantera principer för beräkning

Objektet DataLakeAnalyticsAccountManagementClient ger metoder för att hantera principer för beräkning för ett Data Lake Analytics-konto.

### <a name="list-compute-policies"></a>Lista beräkning principer

Följande kod hämtar en lista över principer för beräkning för ett Data Lake Analytics-konto.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Skapa en ny princip för beräkning

Följande kod skapar en ny princip för beräkning för ett Data Lake Analytics-konto, ange de maximala AU: er tillgängliga till den angivna användaren till mellan 50 och den minsta prioriteten till 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Nästa steg

- Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
- Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).

