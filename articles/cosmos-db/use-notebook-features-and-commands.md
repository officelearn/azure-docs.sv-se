---
title: Använd inbyggda antecknings boks kommandon och funktioner i Azure Cosmos DB
description: Lär dig hur du använder inbyggda kommandon och funktioner för att utföra vanliga åtgärder med hjälp av Azure Cosmos DB inbyggda antecknings böcker.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 1eda8271a3b8aa2c9e247252bd755279d23b6e10
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310352"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Använd inbyggda antecknings boks kommandon och funktioner i Azure Cosmos DB

Inbyggda Jupyter Notebook i Azure Cosmos DB gör att du kan analysera och visualisera dina data från Azure Portal. Den här artikeln beskriver hur du använder inbyggda Notebook-kommandon och-funktioner för att utföra vanliga åtgärder.

## <a name="install-a-new-package"></a>Installera ett nytt paket
När du har aktiverat Notebook support för dina Azure Cosmos-konton kan du öppna en ny antecknings bok och installera ett paket.

Infoga och kör följande kod i en ny Code-cell och Ersätt ``PackageToBeInstalled`` med det önskade python-paketet.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Det här paketet kommer att vara tillgängligt för användning från alla antecknings böcker i Azure Cosmos-kontot. 

## <a name="run-a-sql-query"></a>Kör en SQL-fråga

Du kan använda ``%%sql`` kommandot Magic för att köra en [SQL-fråga](sql-query-getting-started.md) mot en behållare i ditt konto. Använd syntaxen:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Ersätt ``{database_id}`` och``{container_id}`` med namnet på databasen och behållaren i ditt Cosmos-konto. Om argumenten ``--container``ochinte har angetts körs frågan på [standard databasen och-behållaren.](#set-default-database-for-queries) ``--database``
- Du kan köra alla SQL-frågor som är giltiga i Azure Cosmos DB. Frågetexten måste finnas på en ny rad.

Exempel: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Kör ```%%sql?``` i en cell om du vill se hjälp dokumentationen för SQL Magic-kommandot i antecknings boken.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Köra en SQL-fråga och utdata till en Pandas-DataFrame

Du kan generera resultatet av en ``%%sql`` fråga till en Pandas- [DataFrame](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Använd syntaxen: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Ersätt ``{database_id}`` och``{container_id}`` med namnet på databasen och behållaren i ditt Cosmos-konto. Om argumenten ``--container``ochinte har angetts körs frågan på [standard databasen och-behållaren.](#set-default-database-for-queries) ``--database``
- Ersätt ``{outputDataFrameVar}`` med namnet på den DataFrame-variabel som ska innehålla resultaten.
- Du kan köra alla SQL-frågor som är giltiga i Azure Cosmos DB. Frågetexten måste finnas på en ny rad. 

Exempel:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```

## <a name="set-default-database-for-queries"></a>Ange standard databas för frågor
Du kan ange standard databas ```%%sql``` kommandona som ska användas för antecknings boken. Ersätt ```{database_id}``` med namnet på din databas.

```bash
%database {database_id}
```
Kör ```%database?``` i en cell för att se dokumentationen i antecknings boken.

## <a name="set-default-container-for-queries"></a>Ange standard behållare för frågor
Du kan ange standard behållar ```%%sql``` kommandon som ska användas för antecknings boken. Ersätt ```{container_id}``` med namnet på din behållare.

```bash
%container {container_id}
```
Kör ```%container?``` i en cell för att se dokumentationen i antecknings boken.

## <a name="use-the-built-in-python-sdk"></a>Använd den inbyggda python SDK: n
Version 4 av [Azure Cosmos DB python SDK för SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) installeras och ingår i Notebook-miljön för Cosmos-kontot.

Använd den inbyggda ``cosmos_client`` instansen för att köra en SDK-åtgärd. 

Exempel:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Se [python SDK-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> Det inbyggda python SDK stöds endast för SQL (Core) API-konton. För andra API: er måste du [installera den relevanta python-drivrutinen](#install-a-new-package) som motsvarar API: et. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Skapa en anpassad instans av``cosmos_client``
Om du vill ha mer flexibilitet kan du skapa en anpassad ``cosmos_client`` instans av i för att:

- Anpassa [anslutnings principen](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Kör åtgärder mot ett annat Cosmos-konto än det som du befinner dig i

Du kan komma åt anslutnings strängen och primär nyckeln för det aktuella kontot via [miljövariablerna](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Åtkomst till konto slut punkten och primär nyckelns miljö variabler
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Variablerna ``COSMOS_KEY`` och kan bara användas för SQL API. ``COSMOS_ENDPOINT`` För andra API: er hittar du slut punkten och nyckeln i bladet **anslutnings strängar** eller **nycklar** i ditt Cosmos-konto.  

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om fördelarna med att [Azure Cosmos DB Jupyter Notebooks](cosmosdb-jupyter-notebooks.md)
- Läs mer om [Azure Cosmos DB python SDK för SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
