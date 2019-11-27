---
title: Komma åt dataresurser från Jupyter-anteckningsböcker i Azure
description: 'Så här att få åtkomst till filer, REST API: er, databaser och olika Azure Storage-resurser från en Jupyter-anteckningsbok.'
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 23acdf99f6cb69f100e484e236580f3b2b43ba94
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277685"
---
# <a name="access-cloud-data-in-a-notebook"></a>Åtkomst till molndata på en bärbar dator

Utför intressanta arbete i en Jupyter-anteckningsbok kräver att data. Data, faktiskt är livsnerven för bärbara datorer.

Du kan verkligen [importera datafiler till ett projekt](work-with-project-data-files.md), även använda kommandon som `curl` inifrån en bärbar dator för att hämta en fil direkt. Det är troligt, men att du måste arbeta med mycket mer data som är tillgänglig från inte är filbaserad källor, till exempel REST API: er, relationsdatabaser, och molnlagring, till exempel Azure-tabeller.

Den här artikeln beskriver kortfattat de här olika alternativen. Eftersom data åtkomsten fungerar bäst i praktiken kan du hitta körbara-kod i [Azure Notebooks exempel – åtkomst till dina data](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>REST-API:er

De stora mängder data som är tillgänglig från Internet nås generellt sett inte igenom filer, men via REST API: er. Som tur är kan eftersom en notebook cell kan innehålla den kod som kan du använda kod att skicka begäranden och ta emot JSON-data. Du kan sedan konvertera JSON till det format som du vill använda, till exempel en pandas-dataframe.

Du kommer åt data med hjälp av ett REST-API genom att använda samma kod i en anteckningsbok kod celler som du använder i alla andra program. Den allmänna strukturen med hjälp av klientbiblioteket för begäranden är följande:

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

Du kan komma åt SQL Server-databaser med hjälp av pyodbc eller pymssql bibliotek.

[Använd python för att fråga en Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) ger dig instruktioner om hur du skapar en databas som innehåller AdventureWorks-data och hur du frågar efter data. Samma kod visas i exemplet anteckningsboken för den här artikeln.

## <a name="azure-storage"></a>Azure Storage

Azure Storage tillhandahåller flera olika typer av icke-relationell lagring, beroende på vilken typ av data som du har och hur du behöver åtkomst till den:

- Tabellagring: förser låg kostnad, hög volym tabelldata, till exempel insamlade sensorloggarna, diagnostikloggar och så vidare.
- BLOB-lagring: tillhandahåller fil-liknande lagring för alla typer av data.

Exemplet anteckningsboken visar arbetar med både tabeller och blobbar, inklusive hur du använder en signatur för delad åtkomst för att tillåta skrivskyddad åtkomst till BLOB.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB tillhandahåller en helt indexerade NoSQL-lager för JSON-dokument). Följande artiklar innehåller ett antal olika sätt att arbeta med Cosmos DB från Python:

- [Bygg en SQL API-app med python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Bygg en kolv-app med Azure Cosmos DBs API för MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Skapa en graf-databas med python och Gremlin-API: et](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Bygg en Cassandra-app med python och Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Bygg en Tabell-API-app med python och Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

När du arbetar med Cosmos DB kan du använda [Azure-cosmosdb-Table-](https://pypi.org/project/azure-cosmosdb-table/) biblioteket.

## <a name="other-azure-databases"></a>Andra Azure-databaser

Azure tillhandahåller ett antal andra typer av databaser som du kan använda. Artiklarna nedan innehåller riktlinjer för att komma åt dessa databaser från Python:

- [Azure Database for PostgreSQL: Använd python för att ansluta och fråga efter data](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Snabb start: använda Azure Redis Cache med python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL: Använd python för att ansluta och fråga efter data](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Guiden Kopiera för Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Nästa steg

- [Gör så här: arbeta med projektfiler](work-with-project-data-files.md)
