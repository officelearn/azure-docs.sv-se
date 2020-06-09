---
title: Få åtkomst till data i Jupyter Notebooks – Azure Notebooks för hands version
description: 'Lär dig hur du kommer åt filer, REST-API: er, databaser och andra Azure Storage resurser från en Jupyter Notebook.'
ms.topic: how-to
ms.date: 12/04/2018
ms.custom: tracking-python
ms.openlocfilehash: 2c7133e9c258c77aa9c242dd7276076625153703
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84554373"
---
# <a name="access-cloud-data-in-a-notebook"></a>Komma åt molndata i en notebook-fil

Att göra intressanta arbeten i en Jupyter Notebook kräver data. Data är faktiskt Lifeblood-antecknings böcker.

Du kan verkligen [importera datafiler till ett projekt](work-with-project-data-files.md), även använda kommandon som i `curl` en bärbar dator för att hämta en fil direkt. Men det är troligt att du behöver arbeta med mycket mer omfattande data som är tillgängliga från icke-filkällor som REST-API: er, Relations databaser och moln lagring som Azure-tabeller.

I den här artikeln beskrivs de olika alternativen kortfattat. Eftersom data åtkomsten fungerar bäst i praktiken kan du hitta körbara-kod i [Azure Notebooks exempel – åtkomst till dina data](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>REST API:er

Generellt sett kan den stora mängden data som är tillgänglig från Internet nås inte via filer, utan via REST-API: er. Lyckligt vis, eftersom en Notebook-cell kan innehålla vilken kod du gillar, kan du använda kod för att skicka begär Anden och ta emot JSON-data. Du kan sedan konvertera JSON till det format som du vill använda, till exempel en Pandas-dataframe.

Om du vill komma åt data med hjälp av en REST API använder du samma kod i en antecknings boks kod celler som du använder i ett annat program. Den allmänna strukturen med hjälp av begär ande biblioteket är följande:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Azure SQL-databaser

Du kan komma åt SQL Server-databaser med hjälp av pyodbc-eller pymssql-biblioteken.

[Använd python för att fråga en Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) ger dig instruktioner om hur du skapar en databas som innehåller AdventureWorks-data och hur du frågar efter data. Samma kod visas i exempel antecknings boken för den här artikeln.

## <a name="azure-storage"></a>Azure Storage

Azure Storage tillhandahåller flera olika typer av icke-relationell lagring, beroende på vilken typ av data du har och hur du behöver åtkomst till den:

- Table Storage: ger låg kostnad och hög volym lagring för tabell data, till exempel insamlade sensor loggar, diagnostikloggar och så vidare.
- Blob Storage: tillhandahåller filtyper som lagring för alla typer av data.

Exempel antecknings boken visar hur du arbetar med både tabeller och blobbar, inklusive hur du använder en signatur för delad åtkomst för att tillåta skrivskyddad åtkomst till blobbar.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB tillhandahåller en fullständigt indexerad NoSQL-lagring för JSON-dokument. Följande artiklar innehåller ett antal olika sätt att arbeta med Cosmos DB från python:

- [Bygg en SQL API-app med python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Bygg en kolv-app med Azure Cosmos DBs API för MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Skapa en graf-databas med python och Gremlin-API: et](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Skapa en Cassandra-app med Python och Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Skapa en tabell-API-app med Python och Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

När du arbetar med Cosmos DB kan du använda [Azure-cosmosdb-Table-](https://pypi.org/project/azure-cosmosdb-table/) biblioteket.

## <a name="other-azure-databases"></a>Andra Azure-databaser

Azure tillhandahåller ett antal andra databas typer som du kan använda. Artiklarna nedan ger vägledning om hur du kommer åt dessa databaser från python:

- [Azure Database för PostgreSQL: Använda Python för att ansluta och fråga efter data](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Snabbstart: Så här använder du Azure Redis Cache med Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database för MySQL: Använda Python för att ansluta och fråga efter data](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Guiden Kopiera för Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Nästa steg

- [Gör så här: arbeta med projektfiler](work-with-project-data-files.md)
