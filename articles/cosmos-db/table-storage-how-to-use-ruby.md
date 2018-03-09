---
title: "Hur du använder Azure Cosmos DB tabell API: er och Azure Table Storage med Ruby | Microsoft Docs"
description: "Lagra strukturerade data i molnet med hjälp av Azure Table Storage, en NoSQL-databas."
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 02/27/2018
ms.author: mimig
ms.openlocfilehash: 104d793826116462f71e4889386906256b2df8f8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-ruby"></a>Hur du använder Azure Cosmos DB tabell API: er och Azure Table Storage med Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av Azure Table-tjänsten och Azure Cosmos DB tabell API. Exemplen är skrivna i Ruby och använda den [Azure Storage tabell-klientbibliotek för Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Scenarier som tas upp inkluderar **skapar och tar bort en tabell och infoga fråga entiteter i en tabell**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="add-access-to-storage-or-azure-cosmos-db"></a>Lägg till åtkomst till Storage eller Azure Cosmos DB
Om du vill använda Azure Storage eller Azure Cosmos DB, måste du hämtar och använder Ruby Azure-paket som innehåller en uppsättning bekvämlighet bibliotek som kommunicerar med RESTEN av tabellen tjänsterna.

### <a name="use-rubygems-to-obtain-the-package"></a>Använda RubyGems för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac), eller **Bash** (Unix).
2. Typen **symbolen installera azure-lagringstabellen** i kommandofönstret att installera symbolen och beroenden.

### <a name="import-the-package"></a>Importera paketet
Använda valfri textredigerare, lägger du till följande upp i filen Ruby där du tänker använda lagring:

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>Lägg till ett Azure Storage-anslutning
Azure Storage-modulen läser miljövariablerna **AZURE_STORAGE_ACCOUNT** och **AZURE_STORAGE_ACCESS_KEY** information som krävs för att ansluta till ditt Azure Storage-konto. Om de här miljövariablerna inte har angetts måste du ange kontoinformation innan du använder **Azure::Storage::Table::TableService** med följande kod:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Du kan hämta dessa värden från en klassiska eller Resource Manager storage-konto i Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till Storage-konto som du vill använda.
3. I bladet inställningar till höger klickar du på **åtkomstnycklar**.
4. I åtkomst bladet nycklar som visas, visas den åtkomstnyckel 1 och åtkomstnyckel 2. Du kan använda någon av dessa.
5. Klicka på Kopiera-ikonen för att kopiera nyckeln till Urklipp.

## <a name="add-an-azure-cosmos-db-connection"></a>Lägg till en Azure Cosmos DB-anslutning
Kopiera primära anslutningssträngen från Azure-portalen för att ansluta till Azure Cosmos DB och skapa en **klienten** objekt med kopierade anslutningssträngen. Du kan skicka den **klienten** objekt när du skapar en **TableService** objekt:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Skapa en tabell
Den **Azure::Storage::Table::TableService** objekt kan du arbeta med tabeller och de entiteter. Du kan skapa en tabell med de **create_table()** metod. I följande exempel skapar en tabell eller skriver ut felet om sådana finns.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Om du vill lägga till en entitet, först skapa en hash-objekt som definierar egenskaper för enhet. Observera att för varje entitet måste du ange en **PartitionKey** och **RowKey**. Dessa är de unika identifierarna för dina enheter och är värden som kan efterfrågas mycket snabbare än dina andra egenskaper. Azure Storage använder **PartitionKey** att automatiskt distribuera tabellens entiteter över många lagringsnoder. Entiteter med samma **PartitionKey** lagras på samma nod. Den **RowKey** är unikt ID för entiteten i partitionen som den tillhör.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Uppdatera en entitet
Det finns flera metoder för att uppdatera en befintlig entitet:

* **update_entity():** uppdatera en befintlig entitet genom att ersätta den.
* **merge_entity():** uppdaterar en befintlig entitet genom att använda nya egenskapsvärden i befintliga entiteten.
* **insert_or_merge_entity():** uppdaterar en befintlig entitet genom att ersätta den. Om inga entiteten finns infogas en ny:
* **insert_or_replace_entity():** uppdaterar en befintlig entitet genom att använda nya egenskapsvärden i befintliga entiteten. Om inga entiteten finns infogas en ny.

Exemplet nedan visar att uppdatera en entitet med **update_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Med **update_entity()** och **merge_entity()**, om den enhet som du uppdaterar inte finns uppdateringen kommer att misslyckas. Om du vill spara en entitet oavsett om det redan finns du bör därför i stället använda **insert_or_replace_entity()** eller **insert_or_merge_entity()**.

## <a name="work-with-groups-of-entities"></a>Arbeta med grupper av entiteter
Ibland är det praktiskt att skicka flera åtgärder tillsammans i en grupp så atomiska bearbetning av servern. För att åstadkomma som måste du först skapa en **Batch** objekt och sedan använda den **execute_batch()** metod på **TableService**. Exemplet nedan visar skickar två entiteter med RowKey 2 och 3 i en batch. Observera att den endast fungerar för entiteter med samma PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Frågan för en entitet
Om du vill fråga en entitet i en tabell, använder den **get_entity()** metoden genom att skicka namnet på tabellen **PartitionKey** och **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Fråga en uppsättning enheter
Om du vill fråga en uppsättning enheter i en tabell, skapa en fråga hash-objekt och använda den **query_entities()** metod. Exemplet nedan visar att hämta alla entiteter med samma **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Om resultatet är för stort för en enskild fråga att returnera returneras en fortsättningstoken som du kan använda för att hämta de efterföljande sidorna.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper
En fråga till en tabell kan hämta bara några få egenskaper från en entitet. Den här tekniken, kallad ”projektion”, minskar bandbredden och kan förbättra frågeprestanda, särskilt för stora entiteter. Använda select-satsen och ange namnen på de egenskaper som du vill ta till klienten.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Ta bort en entitet
Om du vill ta bort en entitet, Använd den **delete_entity()** metod. Ange namnet på den tabell som innehåller entiteten och PartitionKey RowKey för entiteten.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Ta bort en tabell
Om du vill ta bort en tabell, använder den **delete_table()** metod och pass i tabellen som du vill ta bort.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Nästa steg

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Ruby Developer Center](https://azure.microsoft.com/develop/ruby/)
* [Microsoft Azure Storage tabell-klientbibliotek för Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table) 

