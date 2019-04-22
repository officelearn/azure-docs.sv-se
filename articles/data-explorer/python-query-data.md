---
title: 'Snabbstart: Fråga mot data med hjälp av Python-biblioteket i Azure Data Explorer'
description: I den här snabbstarten får du lära dig hur du frågar efter data i Azure Data Explorer med hjälp av Python.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/16/2018
ms.openlocfilehash: 4de8f68e0384742cea4ce50ccd23a7455b186893
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048749"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>Snabbstart: Fråga mot data med hjälp av Python-biblioteket i Azure Data Explorer

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Data Explorer tillhandahåller ett [dataklientbibliotek för Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Med det här biblioteket kan du fråga efter data från din kod. I den här snabbstarten ansluter du till en tabell i det *hjälpkluster* som vi har skapat som stöd för kursen. Sedan frågar du en tabell i klustret och returnerar resultatet.

Den här snabbstarten är också tillgänglig som en [Azure Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En e-postadress för ett organisationskonto som är medlem i Azure Active Directory (AAD)

* [Python](https://www.python.org/downloads/) installerat på utvecklingsdatorn

## <a name="install-the-data-library"></a>Installera databiblioteket

Installera *azure-kusto-data*.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Lägg till importuttryck och konstanter

Importera klasser från biblioteket, och även dataanalysbiblioteket *Pandas*.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Azure Data Explorer använder ditt AAD-klient-ID för att autentisera ett program. Du hittar ditt klient-ID genom att använda följande URL. Byt ut *YourDomain* mot din domän.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Om din domän till exempel är *contoso.com* blir URL:en: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Klicka på den här URL:en för att se resultatet. Den första raden ser ut så här.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Klient-ID är i det här fallet `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Ange värdet för AAD_TENANT_ID innan du kör den här koden.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

Nu kan du skapa anslutningssträngen. I det här exemplet används enhetsautentisering för åtkomst till klustret. Du kan också använda ett [AAD-programcertifikat](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [en AAD-programnyckel](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20) och [användare och lösenord för AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Ansluta till Azure Data Explorer och köra en fråga

Kör en fråga mot klustret och lagra utdata i en dataram. När den här koden körs returneras ett meddelande som liknar följande: *För att logga in använder du en webbläsare för att öppna sidan https://microsoft.com/devicelogin och anger sedan koden F3W4VWZDM för att autentisera dig*. Följ stegen för att logga in och gå sedan tillbaka för att köra nästa kodblock.

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Utforska data i DataFrame

När du gör en inloggning returnerar frågan ett resultat och det lagras i en dataram. Du kan arbeta med resultaten precis i andra dataramar.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Du bör se de tio främsta resultaten från tabellen StormEvents.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Mata in data med hjälp av Python-biblioteket](python-ingest-data.md) i Azure Data Explorer
