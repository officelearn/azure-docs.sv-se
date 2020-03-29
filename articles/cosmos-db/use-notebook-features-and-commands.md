---
title: Använda inbyggda kommandon och funktioner för bärbara datorer i Azure Cosmos DB (förhandsversion)
description: Lär dig hur du använder inbyggda kommandon och funktioner för att utföra vanliga åtgärder med hjälp av Azure Cosmos DB:s inbyggda anteckningsböcker.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513407"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Använda inbyggda kommandon och funktioner för bärbara datorer i Azure Cosmos DB (förhandsversion)

Inbyggda Jupyter-anteckningsböcker i Azure Cosmos DB gör att du kan analysera och visualisera dina data från Azure-portalen. Den här artikeln beskriver hur du använder inbyggda notebook-kommandon och funktioner till att utföra vanliga åtgärder.

## <a name="install-a-new-package"></a>Installera ett nytt paket
När du har aktiverat stöd för anteckningsbok för dina Azure Cosmos-konton kan du öppna en ny anteckningsbok och installera ett paket.

I en ny kodcell infogar och ``PackageToBeInstalled`` kör du följande kod och ersätter med önskat Python-paket.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Det här paketet är tillgängligt att använda från valfri anteckningsbok på arbetsytan för Azure Cosmos-kontot. 

> [!TIP]
> Om anteckningsboken kräver ett anpassat paket rekommenderar vi att du lägger till en cell i anteckningsboken för att installera paketet, eftersom paket tas bort om du [återställer arbetsytan](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Köra en SQL-fråga

Du kan ``%%sql`` använda det magiska kommandot för att köra en [SQL-fråga](sql-query-getting-started.md) mot valfri behållare i ditt konto. Använd syntaxen:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Ersätt ``{database_id}`` ``{container_id}`` och med namnet på databasen och behållaren i ditt Cosmos-konto. Om ``--database`` argumenten och ``--container`` inte anges körs frågan i [standarddatabasen och behållaren](#set-default-database-for-queries).
- Du kan köra alla SQL-frågor som är giltiga i Azure Cosmos DB. Frågetexten måste finnas på en ny rad.

Ett exempel: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Kör ```%%sql?``` i en cell för att se hjälpdokumentationen för sql magic-kommandot i anteckningsboken.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Köra en SQL-fråga och utdata till en Pandas DataFrame

Du kan mata ut ``%%sql`` resultatet av en fråga till en [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Använd syntaxen: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Ersätt ``{database_id}`` ``{container_id}`` och med namnet på databasen och behållaren i ditt Cosmos-konto. Om ``--database`` argumenten och ``--container`` inte anges körs frågan i [standarddatabasen och behållaren](#set-default-database-for-queries).
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
Du kan ``%%upload`` använda det magiska kommandot för att överföra data från en JSON-fil till en angiven Azure Cosmos-behållare. Använd följande kommando för att ladda upp objekten:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Ersätt ``{database_id}`` ``{container_id}`` och med namnet på databasen och behållaren i ditt Azure Cosmos-konto. Om ``--database`` argumenten och ``--container`` inte anges körs frågan i [standarddatabasen och behållaren](#set-default-database-for-queries).
- Ersätt ``{url_location_of_file}`` med platsen för din JSON-fil. Filen måste vara en matris med giltiga JSON-objekt och den ska vara tillgänglig via det offentliga Internet.

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
Med utdatastatistiken kan du beräkna de effektiva RU/s som används för att ladda upp objekten. Till exempel, om 25.000 RU konsumerades över 38 sekunder, är den effektiva RU / s 25.000 RU / 38 sekunder = 658 RU / s.

## <a name="set-default-database-for-queries"></a>Ange standarddatabas för frågor
Du kan ange ```%%sql``` att standarddatabaskommandon ska användas för anteckningsboken. Ersätt ```{database_id}``` med namnet på databasen.

```bash
%database {database_id}
```
Kör ```%database?``` i en cell för att se dokumentation i anteckningsboken.

## <a name="set-default-container-for-queries"></a>Ange standardbehållare för frågor
Du kan ange ```%%sql``` standardbehållarekommandon som ska användas för anteckningsboken. Ersätt ```{container_id}``` med namnet på behållaren.

```bash
%container {container_id}
```
Kör ```%container?``` i en cell för att se dokumentation i anteckningsboken.

## <a name="use-built-in-nteract-data-explorer"></a>Använda inbyggd nteract-datautforskare
Du kan använda den inbyggda [nteract-datautforskaren](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) för att filtrera och visualisera en DataFrame. Om du vill aktivera ``pd.options.display.html.table_schema`` den ``True`` ``pd.options.display.max_rows`` här funktionen ställer du ``pd.options.display.max_rows`` in ``None`` alternativet på och till önskat värde (du kan ställa in så att alla resultat visas).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract-datautforskare](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Använda den inbyggda Python SDK
Version 4 av [Azure Cosmos DB Python SDK för SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) installeras och ingår i anteckningsboksmiljön för Azure Cosmos-kontot.

Använd den inbyggda ``cosmos_client`` instansen för att köra en SDK-åtgärd. 

Ett exempel:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Se [Python SDK-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> Den inbyggda Python SDK stöds endast för SQL -API-konton (Core). För andra API:er måste du [installera den relevanta Python-drivrutinen](#install-a-new-package) som motsvarar API:et. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Skapa en anpassad instans av``cosmos_client``
För mer flexibilitet kan du skapa ``cosmos_client`` en anpassad instans av för att:

- Anpassa [anslutningsprincipen](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Köra åtgärder mot ett annat Azure Cosmos-konto än det du befinner dig i

Du kan komma åt anslutningssträngen och primärnyckeln för det aktuella kontot via [miljövariablerna](#access-the-account-endpoint-and-primary-key-env-variables). 

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
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Få tillgång till kontoslutpunkt och primärnyckel env variabler
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> ``COSMOS_ENDPOINT`` Variablerna ``COSMOS_KEY`` och miljövariablerna är endast tillämpliga för SQL API. För andra API:er letar du reda på slutpunkten och nyckeln i **bladet Anslutningssträngar** eller **nycklar** i ditt Azure Cosmos-konto.  

## <a name="reset-notebooks-workspace"></a>Arbetsytan Återställ anteckningsböcker
Om du vill återställa arbetsytan för anteckningsböcker till standardinställningarna väljer du **Återställ arbetsytan** i kommandofältet. Detta kommer att ta bort alla anpassade installerade paket och starta om Jupyter-servern. Dina anteckningsböcker, filer och Azure Cosmos-resurser påverkas inte.  

![Arbetsytan Återställ anteckningsböcker](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om fördelarna med [bärbara Azure Cosmos DB Jupyter-datorer](cosmosdb-jupyter-notebooks.md)
- Lär dig mer om [Azure Cosmos DB Python SDK för SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
