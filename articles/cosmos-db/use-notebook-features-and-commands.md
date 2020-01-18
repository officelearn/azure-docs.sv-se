---
title: Använd inbyggda antecknings boks kommandon och funktioner i Azure Cosmos DB (för hands version)
description: Lär dig hur du använder inbyggda kommandon och funktioner för att utföra vanliga åtgärder med hjälp av Azure Cosmos DB inbyggda antecknings böcker.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 2066701a88e03feb29bd1689f36460765f1c638d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264720"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Använd inbyggda antecknings boks kommandon och funktioner i Azure Cosmos DB

Inbyggda Jupyter Notebook i Azure Cosmos DB gör att du kan analysera och visualisera dina data från Azure Portal. Den här artikeln beskriver hur du använder inbyggda notebook-kommandon och funktioner till att utföra vanliga åtgärder.

## <a name="install-a-new-package"></a>Installera ett nytt paket
När du har aktiverat Notebook support för dina Azure Cosmos-konton kan du öppna en ny antecknings bok och installera ett paket.

Infoga och kör följande kod i en ny Code-cell och ersätt ``PackageToBeInstalled`` med det önskade python-paketet.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Det här paketet kommer att vara tillgängligt för användning från alla antecknings böcker i Azure Cosmos Account-arbetsytan. 

> [!TIP]
> Om din bärbara dator kräver ett anpassat paket, rekommenderar vi att du lägger till en cell i din bärbara dator för att installera paketet, eftersom paket tas bort om du [återställer arbets ytan](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Kör en SQL-fråga

Du kan använda kommandot ``%%sql`` Magic för att köra en [SQL-fråga](sql-query-getting-started.md) mot en behållare i ditt konto. Använd syntaxen:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Ersätt ``{database_id}`` och ``{container_id}`` med namnet på databasen och behållaren i ditt Cosmos-konto. Om argumenten ``--database`` och ``--container`` inte anges körs frågan på [standard databasen och-behållaren](#set-default-database-for-queries).
- Du kan köra alla SQL-frågor som är giltiga i Azure Cosmos DB. Frågetexten måste finnas på en ny rad.

Ett exempel: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Kör ```%%sql?``` i en cell om du vill se hjälp dokumentationen för SQL Magic-kommandot i antecknings boken.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Köra en SQL-fråga och utdata till en Pandas-DataFrame

Du kan generera resultatet av en ``%%sql``-fråga till en [Pandas-DataFrame](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Använd syntaxen: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Ersätt ``{database_id}`` och ``{container_id}`` med namnet på databasen och behållaren i ditt Cosmos-konto. Om argumenten ``--database`` och ``--container`` inte anges körs frågan på [standard databasen och-behållaren](#set-default-database-for-queries).
- Ersätt ``{outputDataFrameVar}`` med namnet på den DataFrame-variabel som ska innehålla resultaten.
- Du kan köra alla SQL-frågor som är giltiga i Azure Cosmos DB. Frågetexten måste finnas på en ny rad. 

Ett exempel:

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
## <a name="upload-json-items-to-a-container"></a>Ladda upp JSON-objekt till en behållare
Du kan använda kommandot ``%%upload`` Magic för att överföra data från en JSON-fil till en angiven Azure Cosmos-behållare. Använd följande kommando för att ladda upp objekten:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Ersätt ``{database_id}`` och ``{container_id}`` med namnet på databasen och behållaren i ditt Azure Cosmos-konto. Om argumenten ``--database`` och ``--container`` inte anges körs frågan på [standard databasen och-behållaren](#set-default-database-for-queries).
- Ersätt ``{url_location_of_file}`` med platsen för din JSON-fil. Filen måste vara en matris med giltiga JSON-objekt och den bör vara tillgänglig via det offentliga Internet.

Ett exempel:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Med utmatnings statistik kan du beräkna de faktiska RU/s som används för att överföra objekten. Om till exempel 25 000 ru: er förbrukades över 38 sekunder, är det effektiva RU/s 25 000 ru: er/38 sekunder = 658 RU/s.

## <a name="set-default-database-for-queries"></a>Ange standard databas för frågor
Du kan ange standard databas ```%%sql```-kommandon som ska användas för antecknings boken. Ersätt ```{database_id}``` med namnet på din databas.

```bash
%database {database_id}
```
Kör ```%database?``` i en cell för att se dokumentationen i antecknings boken.

## <a name="set-default-container-for-queries"></a>Ange standard behållare för frågor
Du kan ställa in standard container ```%%sql```-kommandon som ska användas för antecknings boken. Ersätt ```{container_id}``` med namnet på din behållare.

```bash
%container {container_id}
```
Kör ```%container?``` i en cell för att se dokumentationen i antecknings boken.

## <a name="use-built-in-nteract-data-explorer"></a>Använd inbyggda nteract data Explorer
Du kan använda den inbyggda [nteract data Explorer](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) för att filtrera och visualisera en DataFrame. Om du vill aktivera den här funktionen ställer du in alternativet ``pd.options.display.html.table_schema`` till ``True`` och ``pd.options.display.max_rows`` till önskat värde (du kan ställa in ``pd.options.display.max_rows`` till ``None`` att visa alla resultat).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract data Utforskare](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Använd den inbyggda python SDK: n
Version 4 av [Azure Cosmos DB python SDK för SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) installeras och ingår i Notebook-miljön för Azure Cosmos-kontot.

Använd den inbyggda ``cosmos_client``-instansen för att köra en SDK-åtgärd. 

Ett exempel:

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

## <a name="create-a-custom-instance-of-cosmos_client"></a>Skapa en anpassad instans av ``cosmos_client``
För mer flexibilitet kan du skapa en anpassad instans av ``cosmos_client`` för att:

- Anpassa [anslutnings principen](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Kör åtgärder mot ett annat Azure Cosmos-konto än det som du befinner dig i

Du kan komma åt anslutnings strängen och primär nyckeln för det aktuella kontot via [miljövariablerna](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Åtkomst till konto slut punkten och primär nyckelns miljö variabler
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Variablerna ``COSMOS_ENDPOINT`` och ``COSMOS_KEY``-miljö gäller endast för SQL API. För andra API: er hittar du slut punkten och nyckeln i bladet **anslutnings strängar** eller **nycklar** i ditt Azure Cosmos-konto.  

## <a name="reset-notebooks-workspace"></a>Återställ arbets ytan för antecknings böcker
Om du vill återställa arbets ytan för antecknings böcker till standardinställningarna väljer du **Återställ arbets yta** i kommando fältet. Detta tar bort alla anpassade installerade paket och startar om Jupyter-servern. Dina antecknings böcker, filer och Azure Cosmos-resurser kommer inte att påverkas.  

![Återställ arbets ytan för antecknings böcker](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om fördelarna med att [Azure Cosmos DB Jupyter Notebooks](cosmosdb-jupyter-notebooks.md)
- Läs mer om [Azure Cosmos DB python SDK för SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
