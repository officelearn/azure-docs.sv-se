---
title: Använda Azure Table Storage och Azure Cosmos DB Table-API:et med Ruby
description: Lagra strukturerade data i molnet med Azure Table Storage eller Table-API:et för Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 7ddf9c3ef848537cb68dce043bb22680439e9cd5
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877945"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>Använda Azure Table Storage och Azure Cosmos DB Table-API:et med Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Översikt
Den här guiden beskriver hur du utför vanliga scenarier med hjälp av Azure Table Storage och Azure Cosmos DB Table-API:et. Exemplen är skrivna i Ruby och använder [Azure Storage Table-klientbiblioteket för Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Guiden innehåller scenarier som beskriver hur du **skapar och tar bort en tabell och hur du infogar och kör frågor mot entiteter i en tabell**.

## <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-storage-or-azure-cosmos-db"></a>Lägga till åtkomst till Azure Storage eller Azure Cosmos DB
För att kunna använda Azure Storage eller Azure Cosmos DB måste du ladda ned och använda Azure-paketet för Ruby, som innehåller en uppsättning verktygsbibliotek som kommunicerar med Table REST-tjänsterna.

### <a name="use-rubygems-to-obtain-the-package"></a>Hämta paketet med hjälp av RubyGems
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).
2. Installera paketet och beroendena genom att skriva **gem install azure-storage-table**.

### <a name="import-the-package"></a>Importera paketet
Använd valfri textredigerare och lägg till följande kod överst i Ruby-filen där du vill använda Storage:

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>Lägga till en Azure Storage-anslutning
Azure Storage-modulen läser miljövariablerna **AZURE_STORAGE_ACCOUNT** och **AZURE_STORAGE_ACCESS_KEY** och letar efter information som krävs för att ansluta till ditt Azure Storage-konto. Om dessa miljövariabler inte har angetts måste du ange kontoinformationen innan du använder **Azure::Storage::Table::TableService** med följande kod:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Du kan hämta dessa värden från ett klassiskt eller Resource Manager-baserat lagringskonto på Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till det lagringskonto som du vill använda.
3. Klicka på **Åtkomstnycklar** till höger på bladet Inställningar.
4. Åtkomstnyckel 1 och åtkomstnyckel 2 visas på bladet som öppnas. Du kan använda vilken av nycklarna du vill.
5. Kopiera nyckeln till Urklipp genom att klicka på kopieringsikonen.

## <a name="add-an-azure-cosmos-db-connection"></a>Lägga till en Azure Cosmos DB-anslutning
Du ansluter till Azure Cosmos DB genom att kopiera den primära anslutningssträngen från Azure Portal och skapar sedan ett **Client**-objekt med hjälp av den kopierade anslutningssträngen. Du kan definiera **Client**-objektet när du skapar ett **TableService**-objekt:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Skapa en tabell
Du kan arbeta med tabeller och entiteter med objektet **Azure::Storage::Table::TableService**. Du skapar en tabell med hjälp av metoden **create_table()**. Koden i följande exempel skapar en tabell eller returnerar eventuella fel.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
När du lägger till en entitet börjar du med att skapa ett hash-objekt som definierar entitetens egenskaper. Observera att du måste ange en **PartitionKey** och **RowKey** för varje entitet. Dessa är entiteternas unika identifierare. Du kan köra frågor mot dessa värden mycket snabbare än mot andra egenskaper. Azure Storage använder **PartitionKey** för att automatiskt distribuera tabellens entiteter mellan flera lagringsnoder. Entiteter med samma **PartitionKey** lagras på samma nod. **RowKey** är det unika ID:t för entiteten i den partition som den hör till.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Uppdatera en entitet
Du kan uppdatera en befintlig entitet med hjälp av olika metoder:

* **update_entity():** Uppdaterar en befintlig entitet genom att ersätta den.
* **merge_entity():** Uppdaterar en befintlig entitet genom att sammanfoga nya egenskapsvärden i den befintliga entiteten.
* **insert_or_merge_entity():** Uppdaterar en befintlig entitet genom att ersätta den. Om det inte finns någon entitet, infogas en ny:
* **insert_or_replace_entity():** Uppdaterar en befintlig entitet genom att sammanfoga nya egenskapsvärden i den befintliga entiteten. Om det inte finns någon entitet, infogas en ny.

Exemplet nedan visar hur en entitet uppdateras med hjälp av **update_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Åtgärden misslyckas om du använder **update_entity()** eller **merge_entity()** och entiteten som du uppdaterar inte finns. Om du vill lagra en entitet oavsett om den redan finns eller inte bör du därför använda **insert_or_replace_entity()** eller **insert_or_merge_entity()** i stället.

## <a name="work-with-groups-of-entities"></a>Arbeta med grupper av entiteter
Ibland är det praktiskt att skicka flera åtgärder tillsammans i en batch för att säkerställa atomisk bearbetning av servern. För att åstadkomma det skapar du först ett **Batch**-objekt och använder sedan metoden **execute_batch()** i **TableService**. Exemplet nedan visar hur du skickar två entiteter med RowKey 2 och 3 i en batch. Observera att detta endast fungerar för entiteter med samma PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Fråga efter en entitet
Om du vill fråga efter en entitet i en tabell använder du metoden **get_entity()** genom att skicka tabellnamnet, **PartitionKey** och **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Fråga efter en uppsättning entiteter
Om du vill fråga efter en uppsättning enheter i en tabell skapar du ett hash-objekt för frågan och använder metoden **query_entities()**. Exemplet nedan visar hur du hämtar alla entiteter med samma **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Om resultatuppsättningen är för stor och en enskild fråga inte kan returnera hela uppsättningen returneras en fortsättningstoken som du kan använda för att hämta de efterföljande sidorna.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper
En fråga till en tabell kan hämta några få egenskaper från en entitet. Den här tekniken, kallad ”projektion”, minskar bandbredden och kan förbättra frågeprestanda, i synnerhet för stora entiteter. Använd select-satsen och ange namnen på de egenskaper som du vill skicka till klienten.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Ta bort en entitet
Om du vill ta bort en entitet använder du metoden **delete_entity()**. Ange namnet på tabellen som innehåller entiteten, samt PartitionKey och RowKey för entiteten.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Ta bort en tabell
Om du vill ta bort en tabell använder du metoden **delete_table()** och anger namnet på den tabell som du vill ta bort.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Nästa steg

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Ruby Developer Center](https://azure.microsoft.com/develop/ruby/)
* [Microsoft Azure Storage Table-klientbiblioteket för Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table) 

