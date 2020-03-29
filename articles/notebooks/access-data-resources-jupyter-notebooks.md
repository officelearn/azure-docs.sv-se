---
title: Komma åt data i Jupyter-anteckningsböcker – förhandsversionen av Azure-anteckningsböcker
description: Lär dig hur du kommer åt filer, REST-API:er, databaser och olika Azure Storage-resurser från en Jupyter-anteckningsbok.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 47d2f869021851c1451a66a84b1a70ec4ff4998f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646355"
---
# <a name="access-cloud-data-in-a-notebook"></a>Komma åt molndata i en notebook-fil

Att göra intressant arbete i en Jupyter anteckningsbok kräver data. Data är faktiskt livsnerven i anteckningsböcker.

Du kan säkert [importera datafiler till ett](work-with-project-data-files.md) `curl` projekt , även med kommandon som inifrån en anteckningsbok för att ladda ner en fil direkt. Det är dock troligt att du måste arbeta med mycket mer omfattande data som är tillgängliga från icke-filkällor som REST API:er, relationsdatabaser och molnlagring, till exempel Azure-tabeller.

I den här artikeln beskrivs kortfattat dessa olika alternativ. Eftersom dataåtkomst bäst visas i praktiken kan du hitta körbar kod i [Azure Notebooks Samples - Access your data](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>REST API:er

Generellt sett är den stora mängden data som är tillgängliga från Internet nås inte via filer, men genom REST API: er. Lyckligtvis, eftersom en anteckningsbok cell kan innehålla vilken kod du vill, kan du använda kod för att skicka förfrågningar och ta emot JSON-data. Du kan sedan konvertera den JSON till vilket format du vill använda, till exempel en pandas dataframe.

Om du vill komma åt data med hjälp av ett REST API använder du samma kod i en anteckningsboks kodceller som du använder i något annat program. Den allmänna strukturen som använder biblioteket för begäranden är följande:

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

Du kan komma åt SQL Server-databaser med hjälp av pyodbc- eller pymssql-biblioteken.

[Använd Python för att fråga en Azure SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) ger dig instruktioner om hur du skapar en databas som innehåller AdventureWorks-data och visar hur du frågar dessa data. Samma kod visas i exempelanteckningsboken för den här artikeln.

## <a name="azure-storage"></a>Azure Storage

Azure Storage innehåller flera olika typer av icke-relationslagring, beroende på vilken typ av data du har och hur du behöver komma åt dem:

- Tabelllagring: tillhandahåller billig lagring med stora volymer för tabelldata, till exempel insamlade sensorloggar, diagnostikloggar och så vidare.
- Blob-lagring: tillhandahåller filliknande lagring för alla typer av data.

Exempelanteckningsboken visar hur du arbetar med både tabeller och blobbar, inklusive hur du använder en signatur för delad åtkomst för att tillåta skrivskyddad åtkomst till blobbar.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB tillhandahåller ett fullständigt indexerat NoSQL-arkiv för JSON-dokument). Följande artiklar innehåller ett antal olika sätt att arbeta med Cosmos DB från Python:

- [Skapa en SQL API-app med Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Skapa en Flask-app med Azure Cosmos DB:s API för MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Skapa en grafdatabas med Python och Gremlin-API:et](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Skapa en Cassandra-app med Python och Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Skapa en tabell-API-app med Python och Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

När du arbetar med Cosmos DB kan du använda [azure-cosmosdb-table-biblioteket.](https://pypi.org/project/azure-cosmosdb-table/)

## <a name="other-azure-databases"></a>Andra Azure-databaser

Azure innehåller ett antal andra databastyper som du kan använda. Artiklarna nedan ger vägledning för åtkomst till dessa databaser från Python:

- [Azure Database för PostgreSQL: Använda Python för att ansluta och fråga efter data](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Snabbstart: Så här använder du Azure Redis Cache med Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database för MySQL: Använda Python för att ansluta och fråga efter data](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Kopiera guiden för Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Nästa steg

- [Så här: Arbeta med projektdatafiler](work-with-project-data-files.md)
