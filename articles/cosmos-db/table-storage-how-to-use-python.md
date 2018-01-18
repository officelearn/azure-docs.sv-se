---
title: "Hur du använder Azure Table storage med Python | Microsoft Docs"
description: "Lagra strukturerade data i molnet med hjälp av Azure Table Storage, en NoSQL-databas."
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: a786f82d94a1a0039ed65a618670f872ffa3e3c2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="how-to-use-azure-table-storage-with-python"></a>Hur du använder Azure Table storage med Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Den här guiden visar hur du utför vanliga scenarier för Azure Table storage i Python med hjälp av den [Microsoft Azure Storage SDK för Python](https://github.com/Azure/azure-storage-python). Scenarier som tas upp är hur du skapar och tar bort en tabell och infoga fråga entiteter.

När du arbetar via scenarier i den här kursen kan du referera till den [Azure Storage SDK för Python API-referens för](https://azure-storage.readthedocs.io/en/latest/index.html).

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="install-the-azure-storage-sdk-for-python"></a>Installera Azure Storage SDK för Python

När du har skapat ett lagringskonto, nästa steg är att installera den [Microsoft Azure Storage SDK för Python](https://github.com/Azure/azure-storage-python). Mer information om hur du installerar SDK finns i den [README.rst](https://github.com/Azure/azure-storage-python/blob/master/README.rst) filen i Storage SDK: N för Python lagringsplats på GitHub.

## <a name="create-a-table"></a>Skapa en tabell

Om du vill arbeta med Azure Table-tjänsten i Python, måste du importera den [TableService] [ py_TableService] modul. Eftersom du arbetar med tabellentiteter, behöver du också den [entiteten] [ py_Entity] klass. Lägg till den här koden längst upp Python-fil om du vill importera båda:

```python
from azure.storage.table import TableService, Entity
```

Skapa en [TableService] [ py_TableService] objekt som passerar i din namn och åtkomstnyckel för lagring. Ersätt `myaccount` och `mykey` med kontonamnet och nyckeln och anropet [create_table] [ py_create_table] att skapa tabellen i Azure Storage.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

Om du vill lägga till en enhet måste du först skapa ett objekt som representerar din enhet och sedan skicka objekt till den [TableService][py_TableService].[ insert_entity] [ py_insert_entity] metod. Entitetsobjektet kan vara en ordlista eller ett objekt av typen [entiteten][py_Entity], och definierar egenskapsnamn och värden för din enhet. Varje entitet måste innehålla de nödvändiga [PartitionKey och RowKey](#partitionkey-and-rowkey) egenskaper, utöver andra egenskaper som du definierar för entiteten.

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
from azure.storage.table import TableBatch
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

Ta bort en enhet genom att ange dess PartitionKey och RowKey till den [delete_entity] [ py_delete_entity] metod.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Ta bort en tabell

Om du inte längre behöver en tabell eller någon av enheterna i den anropar den [delete_table] [ py_delete_table] metod för att ta bort tabellen från Azure Storage.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Nästa steg

* [Azure Storage-SDK för Python API-referens](https://azure-storage.readthedocs.io/en/latest/index.html)
* [Azure Storage SDK för Python](https://github.com/Azure/azure-storage-python)
* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md): ett kostnadsfritt, plattformsoberoende program för att visuellt arbeta med Azure Storage-data i Windows, macOS och Linux.

[py_commit_batch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.commit_batch
[py_create_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.create_table
[py_delete_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_entity
[py_delete_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_table
[py_Entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.models.html#azure.storage.table.models.Entity
[py_get_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.get_entity
[py_insert_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_entity
[py_insert_or_replace_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_or_replace_entity
[py_merge_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.merge_entity
[py_update_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.update_entity
[py_TableService]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html
[py_TableBatch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tablebatch.html#azure.storage.table.tablebatch.TableBatch