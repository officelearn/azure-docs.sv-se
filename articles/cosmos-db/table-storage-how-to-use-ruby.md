---
title: "Hur du använder Azure Table Storage från Ruby | Microsoft Docs"
description: "Lagra strukturerade data i molnet med hjälp av Azure Table Storage, en NoSQL-databas."
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.openlocfilehash: 372bc89f75ad4730f0defbf9d6f9f041ae5ce1bf
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-use-azure-table-storage-from-ruby"></a>Hur du använder Azure Table Storage från Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av tjänsten Azure Table. Exemplen är skrivna med hjälp av Ruby-API. Scenarier som tas upp inkluderar **skapar och tar bort en tabell, lägga till och fråga entiteter i en tabell**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Skapa ett Ruby-program
Mer information hur du skapar ett Ruby program finns [Ruby på spår webbprogram på en Azure VM](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program åtkomst till lagring
För att använda Azure Storage, måste du hämta och använda Ruby azure-paketet som innehåller en uppsättning bekvämlighet bibliotek som kommunicerar med Storage REST-tjänster.

### <a name="use-rubygems-to-obtain-the-package"></a>Använda RubyGems för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac), eller **Bash** (Unix).
2. Typen **symbolen installera azure** i kommandofönstret att installera symbolen och beroenden.

### <a name="import-the-package"></a>Importera paketet
Använda valfri textredigerare, lägger du till följande upp i filen Ruby där du tänker använda lagring:

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Skapa en Azure Storage-anslutning
Azure-modulen läses miljövariablerna **AZURE\_lagring\_konto** och **AZURE\_lagring\_åtkomst\_NYCKELN**information som krävs för att ansluta till ditt Azure Storage-konto. Om de här miljövariablerna inte har angetts måste du ange kontoinformation innan du använder **Azure::TableService** med följande kod:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Du kan hämta dessa värden från en klassiska eller Resource Manager storage-konto i Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till storage-konto som du vill använda.
3. I bladet inställningar till höger klickar du på **åtkomstnycklar**.
4. I åtkomst bladet nycklar som visas, visas den åtkomstnyckel 1 och åtkomstnyckel 2. Du kan använda någon av dessa.
5. Klicka på Kopiera-ikonen för att kopiera nyckeln till Urklipp.

## <a name="create-a-table"></a>Skapa en tabell
Den **Azure::TableService** objekt kan du arbeta med tabeller och de entiteter. Du kan skapa en tabell med de **skapa\_table()** metod. I följande exempel skapar en tabell eller skriver ut felet om sådana finns.

```ruby
azure_table_service = Azure::TableService.new
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

* **Uppdatera\_entity():** uppdatera en befintlig entitet genom att ersätta den.
* **merge\_entity():** uppdaterar en befintlig entitet genom att använda nya egenskapsvärden i befintliga entiteten.
* **Infoga\_eller\_merge\_entity():** uppdaterar en befintlig entitet genom att ersätta den. Om inga entiteten finns infogas en ny:
* **Infoga\_eller\_ersätta\_entity():** uppdaterar en befintlig entitet genom att använda nya egenskapsvärden i befintliga entiteten. Om inga entiteten finns infogas en ny.

Exemplet nedan visar att uppdatera en entitet med **uppdatera\_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Med **uppdatera\_entity()** och **merge\_entity()**, om den enhet som du uppdaterar inte finns uppdateringen kommer att misslyckas. Därför om du vill lagra en entitet oavsett om den redan finns, bör du istället använda **infoga\_eller\_ersätta\_entity()** eller **infoga\_eller \_merge\_entity()**.

## <a name="work-with-groups-of-entities"></a>Arbeta med grupper av entiteter
Ibland är det praktiskt att skicka flera åtgärder tillsammans i en grupp så atomiska bearbetning av servern. För att åstadkomma som måste du först skapa en **Batch** objekt och sedan använda den **köra\_batch()** metod på **TableService**. Exemplet nedan visar skickar två entiteter med RowKey 2 och 3 i en batch. Observera att den endast fungerar för entiteter med samma PartitionKey.

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
Om du vill fråga en entitet i en tabell, använder den **hämta\_entity()** metoden genom att skicka namnet på tabellen **PartitionKey** och **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Fråga en uppsättning enheter
Om du vill fråga en uppsättning enheter i en tabell, skapa en fråga hash-objekt och använda den **frågan\_entities()** metod. Exemplet nedan visar att hämta alla entiteter med samma **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Om resultatet är för stort för en enskild fråga att returnera en fortsättningstoken returneras som du kan använda för att hämta de efterföljande sidorna.
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
Om du vill ta bort en entitet, Använd den **ta bort\_entity()** metod. Du måste ange namnet på den tabell som innehåller entiteten PartitionKey och RowKey för entiteten.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Ta bort en tabell
Om du vill ta bort en tabell, använder den **ta bort\_table()** metod och pass i tabellen som du vill ta bort.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Nästa steg

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Azure SDK för Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) databasen på GitHub

