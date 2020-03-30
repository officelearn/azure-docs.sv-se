---
title: Hantera Azure Data Lake Analytics med Python
description: I den här artikeln beskrivs hur du använder Python för att hantera DataSjöanalyskonton, datakällor, användare & jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d40658e1510c9ae8a2e3e1f865df7ac95f61abfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68355977"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Hantera Azure Data Lake Analytics med Python
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

I den här artikeln beskrivs hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av Python.

## <a name="supported-python-versions"></a>Python-versioner som stöds

* Använd en 64-bitarsversion av Python.
* Du kan använda standarddistributionen av Python som finns på **[Python.org nedladdningar](https://www.python.org/downloads/)**. 
* Många utvecklare tycker att det är bekvämt att använda **[Anaconda Python-distributionen](https://www.anaconda.com/download/)**.  
* Den här artikeln skrevs med Python version 3.6 från standarddistributionen av Python

## <a name="install-azure-python-sdk"></a>Installera Azure Python SDK

Installera följande moduler:

* **Azure-mgmt-resource-modulen** innehåller andra Azure-moduler för Active Directory, etc.
* **Azure-datalake-store-modulen** innehåller azure datasjölagringsfilsystemåtgärder. 
* **Azure-mgmt-datalake-store-modulen** innehåller azure data lake store-kontohanteringsåtgärder.
* **Azure-mgmt-datalake-analytics-modulen** innehåller Azure Data Lake Analytics-åtgärderna. 

Kontrollera först att du `pip` har det senaste genom att köra följande kommando:

```
python -m pip install --upgrade pip
```

Det här dokumentet `pip version 9.0.1`skrevs med .

Använd följande `pip` kommandon för att installera modulerna från kommandoraden:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Skapa ett nytt Python-skript

Klistra in följande kod i skriptet:

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

Kör det här skriptet för att kontrollera att modulerna kan importeras.

## <a name="authentication"></a>Autentisering

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interaktiv användarautentisering med ett popup-fönster

Den här metoden stöds inte.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktiv användarautentisering med en enhetskod

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Icke-interaktiv autentisering med SPI och en hemlighet

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Icke-interaktiv autentisering med API och ett certifikat

Den här metoden stöds inte.

## <a name="common-script-variables"></a>Vanliga skriptvariabler

Dessa variabler används i proverna.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Skapa klienterna

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Skapa en Azure-resursgrupp

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto

Skapa först ett butikskonto.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Skapa sedan ett ADLA-konto som använder arkivet.

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

## <a name="wait-for-a-job-to-end"></a>Vänta tills ett jobb har avslutats

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Lista pipelines och upprepningar
Beroende på om dina jobb har kopplade pipeline- eller återkommande metadata kan du lista pipelines och upprepningar.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Hantera beräkningsprinciper

DataLakeAnalyticsAccountManagementClient-objektet innehåller metoder för att hantera beräkningsprinciperna för ett DataSjöanalyskonto.

### <a name="list-compute-policies"></a>Lista beräkningsprinciper

Följande kod hämtar en lista över beräkningsprinciper för ett Data Lake Analytics-konto.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Skapa en ny beräkningsprincip

Följande kod skapar en ny beräkningsprincip för ett DataSjöanalyskonto, som anger den maximala AUs som är tillgänglig för den angivna användaren till 50 och den lägsta jobbprioriteten till 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Nästa steg

- Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
- Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med Azure-portalen](data-lake-analytics-manage-use-portal.md).

