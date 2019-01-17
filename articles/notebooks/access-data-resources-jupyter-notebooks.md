---
title: Komma åt dataresurser från Jupyter-anteckningsböcker i Azure
description: 'Så här att få åtkomst till filer, REST API: er, databaser och olika Azure Storage-resurser från en Jupyter-anteckningsbok.'
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 0e3022296dd1a3e1221bb44c94df787660df2a70
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359892"
---
# <a name="access-cloud-data-in-a-notebook"></a>Åtkomst till molndata på en bärbar dator

Utför intressanta arbete i en Jupyter-anteckningsbok kräver att data. Data, faktiskt är livsnerven för bärbara datorer.

Du kan däremot [importera datafiler i ett projekt](work-with-project-data-files.md), även med hjälp av kommandon som `curl` från inom en anteckningsboken för att hämta en fil direkt. Det är troligt, men att du måste arbeta med mycket mer data som är tillgänglig från inte är filbaserad källor, till exempel REST API: er, relationsdatabaser, och molnlagring, till exempel Azure-tabeller.

Den här artikeln beskriver kortfattat de här olika alternativen. Eftersom dataåtkomst visas bäst i praktiken, hittar du att köra flödet koden i den [anteckningsböcker i Azure-exempel – åtkomst till dina data](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>REST API:er

De stora mängder data som är tillgänglig från Internet nås generellt sett inte igenom filer, men via REST API: er. Som tur är kan eftersom en notebook cell kan innehålla den kod som kan du använda kod att skicka begäranden och ta emot JSON-data. Du kan sedan konvertera JSON till det format som du vill använda, till exempel en pandas-dataframe.

Du kommer åt data med hjälp av ett REST-API genom att använda samma kod i en anteckningsbok kod celler som du använder i alla andra program. Den allmänna strukturen med hjälp av klientbiblioteket för begäranden är följande:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit" : "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Azure SQL-databaser

Du kan komma åt SQL Server-databaser med hjälp av pyodbc eller pymssql bibliotek.

[Använda Python för att fråga en Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) innehåller anvisningar om hur du skapar en databas som innehåller AdventureWorks data och visar hur du fråga efter dessa data. Samma kod visas i exemplet anteckningsboken för den här artikeln.

## <a name="azure-storage"></a>Azure Storage

Azure Storage tillhandahåller flera olika typer av icke-relationell lagring, beroende på vilken typ av data som du har och hur du behöver åtkomst till den:

- Tabellagring: förser låg kostnad, hög volym tabelldata, till exempel insamlade sensorloggarna, diagnostikloggar och så vidare.
- BLOB-lagring: tillhandahåller fil-liknande lagring för alla typer av data.

Exemplet anteckningsboken visar arbetar med både tabeller och blobbar, inklusive hur du använder en signatur för delad åtkomst för att tillåta skrivskyddad åtkomst till BLOB.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB tillhandahåller en helt indexerade NoSQL-lager för JSON-dokument). Följande artiklar innehåller ett antal olika sätt att arbeta med Cosmos DB från Python:

- [Skapa en SQL API-app med Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Skapa en Flask-app med Azure Cosmos DB: s API för MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Skapa en grafdatabas med Python och Gremlin-API](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Skapa en Cassandra-app med Python och Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Skapa en tabell-API-app med Python och Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

När du arbetar med Cosmos DB kan du använda den [azure-cosmos DB-table](https://pypi.org/project/azure-cosmosdb-table/) biblioteket.

## <a name="other-azure-databases"></a>Andra Azure-databaser

Azure tillhandahåller ett antal andra typer av databaser som du kan använda. Artiklarna nedan innehåller riktlinjer för att komma åt dessa databaser från Python:

- [Azure Database för PostgreSQL: Använd Python för att ansluta och fråga efter data](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Snabbstart: Använda Azure Redis Cache med Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database för MySQL: Använd Python för att ansluta och fråga efter data](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Kopieringsguiden för Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Nästa steg

- [Anvisningar: Arbeta med data projektfiler](work-with-project-data-files.md)
