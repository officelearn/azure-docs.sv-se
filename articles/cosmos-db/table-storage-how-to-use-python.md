---
title: Kom igång med Azure Table storage med hjälp av Python | Microsoft Docs
description: Lagra strukturerade data i molnet med hjälp av Azure Table Storage, en NoSQL-databas.
services: cosmos-db
documentationcenter: python
author: SnehaGunda
manager: kfile
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: c8f35656e9db07b596cd24ecb570fa0960f540b8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-azure-table-storage-using-python"></a>Kom igång med Azure Table storage med hjälp av Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage är en tjänst som lagrar strukturerade NoSQL-data i molnet och som ger tillgång till ett nyckel-/attributlager med en schemalös design. Eftersom Table Storage är schemalös är det enkelt att anpassa dina data i takt med att programmets behov förändras. Åtkomsten till data i Table Storage är snabb och kostnadseffektiv för många typer av program, och medför normalt lägre kostnad än traditionell SQL för liknande datavolymer.

Du kan använda Table Storage för att lagra flexibla datauppsättningar som användardata för webbprogram, adressböcker, enhetsinformation eller andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

### <a name="about-this-tutorial"></a>Om den här självstudiekursen
Den här kursen visar hur du använder den [Cosmos Azure DB tabell SDK för Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) i vanliga scenarier för Azure Table storage. Namnet på SDK anger för användning med Azure Cosmos DB, men den fungerar med både Azure-Cosmos-DB och varje service i Azure-tabeller lagring bara har en unik slutpunkt. Dessa scenarier beskrivs med Python-exempel som illustrerar hur du:
* Skapa och ta bort tabeller
* Infoga och fråga entiteter
* Ändra entiteter

När du arbetar via scenarier i den här kursen får du vill referera till den [Azure Cosmos DB SDK för Python API-referens för](https://azure.github.io/azure-cosmosdb-python/).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra den här kursen:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 eller 3,6
- [Azure Cosmos DB tabell SDK för Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Detta SDK ansluter med både Azure Table storage och Azure Cosmos DB tabell API.
- [Azure Storage-konto](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account#create-a-storage-account) eller [Azure DB som Cosmos-konto](https://azure.microsoft.com/en-us/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Skapa ett konto i Azure-tjänst
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett Azure Cosmos DB tabell API-konto
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Installera Azure Cosmos-databastabellen SDK för Python

När du har skapat ett lagringskonto, nästa steg är att installera den [Microsoft Azure Cosmos DB tabell SDK för Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Mer information om hur du installerar SDK finns i den [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) filen i Cosmos DB tabell SDK för Python-databasen på GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importera klasserna TableService och entitet

Om du vill arbeta med enheter i Azure Table-tjänsten i Python, som du använder den [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) och [entiteten] [ py_Entity] klasser. Lägg till den här koden längst upp Python-fil om du vill importera båda:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Ansluta till Azure Table-tjänsten

För att ansluta till Azure Storage tabelltjänsten, skapa en [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) objekt och skicka in din namn och åtkomstnyckel för lagring. Ersätt `myaccount` och `mykey` med kontonamnet och nyckeln.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Ansluta till Azure Cosmos DB

Kopiera primära anslutningssträngen från Azure-portalen för att ansluta till Azure Cosmos DB och skapa en [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) objekt med kopierade anslutningssträngen:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Skapa en tabell

Anropa [create_table] [ py_create_table] att skapa tabellen.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

Om du vill lägga till en enhet måste du först skapa ett objekt som representerar din enhet och sedan skicka objekt till den [TableService.insert_entity metoden][py_TableService]. Entitetsobjektet kan vara en ordlista eller ett objekt av typen [entiteten][py_Entity], och definierar egenskapsnamn och värden för din enhet. Varje entitet måste innehålla de nödvändiga [PartitionKey och RowKey](#partitionkey-and-rowkey) egenskaper, utöver andra egenskaper som du definierar för entiteten.

Det här exemplet skapar ett katalogobjekt som representerar en entitet sedan skickar det till den [insert_entity] [ py_insert_entity] metod för att lägga till den i tabellen:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

Det här exemplet skapas en [entiteten] [ py_Entity] objekt och skickar det till den [insert_entity] [ py_insert_entity] metod för att lägga till den i tabellen:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey och RowKey

Du måste ange både en **PartitionKey** och en **RowKey** egenskap för varje entitet. Dessa är de unika identifierarna på entiteter, som tillsammans de bildar primärnyckel för en entitet. Du kan fråga med hjälp av dessa värden som är mycket snabbare än du kan fråga andra Entitetsegenskaper eftersom endast dessa egenskaper indexeras.

Tjänsten använder tabellen **PartitionKey** Intelligent distribuerar tabellentiteter över lagringsnoder. Enheter som har samma **PartitionKey** lagras på samma nod. **RowKey** är unikt ID för entiteten i partitionen som den tillhör.

## <a name="update-an-entity"></a>Uppdatera en entitet

Om du vill uppdatera alla egenskapsvärden för en entitet, anropa den [update_entity] [ py_update_entity] metod. Det här exemplet visar hur du ersätter en befintlig entitet med en uppdaterad version:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

Uppdateringen misslyckas om entiteten som ska uppdateras inte redan finns. Om du vill lagra en entitet om den finns eller inte kan använda [insert_or_replace_entity][py_insert_or_replace_entity]. I följande exempel är ersätter det första anropet befintliga entiteten. Det andra anropet ska infoga en ny enhet, eftersom ingen entitet med angivna PartitionKey och RowKey finns i tabellen.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Den [update_entity] [ py_update_entity] metoden ersätter alla egenskaper och värden för en befintlig enhet där du kan också ta bort egenskaper från en befintlig entitet. Du kan använda den [merge_entity] [ py_merge_entity] metod för att uppdatera en befintlig entitet med nya eller ändrade egenskapsvärden utan att helt ersätta entiteten.

## <a name="modify-multiple-entities"></a>Ändra flera entiteter

Du kan skicka flera åtgärder tillsammans i en grupp för att säkerställa atomiska bearbetningen av en begäran av tabelltjänsten. Använd först den [TableBatch] [ py_TableBatch] klassen om du vill lägga till flera åtgärder i en enskild batch. Därefter anropar [TableService][py_TableService].[ commit_batch] [ py_commit_batch] att skicka åtgärder i en atomisk åtgärd. Alla enheter som ska ändras i batchen måste finnas i samma partition.

Det här exemplet lägger till två entiteter tillsammans i en batch:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Batchar kan också användas med kontexten manager syntax:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Frågan för en entitet

Om du vill fråga för en entitet i en tabell, skickar dess PartitionKey och RowKey till den [TableService][py_TableService].[ get_entity] [ py_get_entity] metod.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Fråga en uppsättning enheter

Du kan fråga efter en uppsättning enheter genom att ange en filtreringssträng med den **filter** parameter. Det här exemplet hittar du alla aktiviteter i Seattle genom att använda ett filter på PartitionKey:

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper

Du kan också begränsa vilka egenskaper som returneras för varje entitet i en fråga. Den här tekniken, kallad *projektion*, minskar bandbredden och kan förbättra frågeprestanda, särskilt för stora entiteter eller resultatmängder. Använd den **Välj** parameter- och pass namnen på egenskaperna som returneras till klienten.

Frågan i följande kod returnerar bara beskrivningar av entiteter i tabellen.

> [!NOTE]
> Följande kodavsnitt fungerar bara mot Azure Storage. Det stöds inte av storage-emulatorn.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Ta bort en entitet

Ta bort en enhet genom att ange dess **PartitionKey** och **RowKey** till den [delete_entity] [ py_delete_entity] metod.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Ta bort en tabell

Om du inte längre behöver en tabell eller någon av enheterna i den anropar den [delete_table] [ py_delete_table] metod för att ta bort tabellen från Azure Storage.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar - utveckla med tabell-API](https://docs.microsoft.com/en-us/azure/cosmos-db/faq#develop-with-the-table-api)
* [Azure DB Cosmos-SDK för Python API-referens](https://azure.github.io/azure-cosmosdb-python/)
* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md): ett kostnadsfritt, plattformsoberoende program för att visuellt arbeta med Azure Storage-data i Windows, macOS och Linux.
* [Arbeta med Python i Visual Studio (Windows)](https://docs.microsoft.com/en-us/visualstudio/python/overview-of-python-tools-for-visual-studio)

[py_commit_batch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_create_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_Entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.models.html
[py_get_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_or_replace_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableService]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableBatch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tablebatch.html
[py_merge_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_update_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
