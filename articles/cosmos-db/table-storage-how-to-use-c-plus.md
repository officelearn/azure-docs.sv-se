---
title: Så använder du Azure Table Storage och Azure Cosmos DB Table API med C++
description: Lagra strukturerade data i molnet med Azure Table Storage eller Azure Cosmos DB Table API.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: b8fa0a3cebd87f4da1a47c605ba21b0cb10a2517
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220052"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Använda Azure Table Storage och Azure Cosmos DB Table-API:et med C++

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

This guide shows you common scenarios by using the Azure Table storage service or Azure Cosmos DB Table API. Exemplen är skrivna i C++ och använder [Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). This article covers the following scenarios:

* Create and delete a table
* Work with table entities

> [!NOTE]
> För den här guiden krävs Azure Storage-klientbiblioteket för C++ version 1.0.0 eller senare. The recommended version is Storage Client Library 2.2.0, which is available by using [NuGet](https://www.nuget.org/packages/wastorage) or [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Create accounts

### <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett konto för Table-API:et för Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program

In this guide, you use storage features from a C++ application. To do so, install the Azure Storage Client Library for C++.

To install the Azure Storage Client Library for C++, use the following methods:

* **Linux:** Follow the instructions given in the [Azure Storage Client Library for C++ README: Getting Started on Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) page.
* **Windows:** On Windows, use [vcpkg](https://github.com/microsoft/vcpkg) as the dependency manager. Follow the [quick-start](https://github.com/microsoft/vcpkg#quick-start) to initialize vcpkg. Then, use the following command to install the library:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

You can find a guide for how to build the source code and export to Nuget in the [README](https://github.com/Azure/azure-storage-cpp#download--install) file.

### <a name="configure-access-to-the-table-client-library"></a>Konfigurera åtkomst till klientbiblioteket för Table Storage

To use the Azure storage APIs to access tables, add the following `include` statements to the top of the C++ file:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Azure Storage- och Cosmos DB-klienter använder en anslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst till datahanteringstjänster. When you run a client application, you must provide the storage connection string or Azure Cosmos DB connection string in the appropriate format.

### <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en Azure Storage-anslutningssträng

This example shows how to declare a static field to hold the Azure Storage connection string:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Use the name of your Storage account for `<your_storage_account>`. For <your_storage_account_key>, use the access key for the Storage account listed in the [Azure portal](https://portal.azure.com). For information on Storage accounts and access keys, see [Create a storage account](../storage/common/storage-create-storage-account.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Konfigurera en Azure Cosmos DB-anslutningssträng

This example shows how to declare a static field to hold the Azure Cosmos DB connection string:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Use the name of your Azure Cosmos DB account for `<your_cosmos_db_account>`. Enter your primary key for `<your_cosmos_db_account_key>`. Enter the endpoint listed in the [Azure portal](https://portal.azure.com) for `<your_cosmos_db_endpoint>`.

To test your application in your local Windows-based computer, you can use the Azure storage emulator that is installed with the [Azure SDK](https://azure.microsoft.com/downloads/). Azure Storage-emulatorn är ett verktyg som simulerar tjänsterna Azure Blob, Queue och Table som finns på den lokala utvecklingsdatorn. The following example shows how to declare a static field to hold the connection string to your local storage emulator:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

To start the Azure storage emulator, from your Windows desktop, select the **Start** button or the Windows key. Enter and run *Microsoft Azure Storage Emulator*. Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Hämta anslutningssträngen

You can use the `cloud_storage_account` class to represent your storage account information. To retrieve your storage account information from the storage connection string, use the `parse` method.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Next, get a reference to a `cloud_table_client` class. This class lets you get reference objects for tables and entities stored within the Table storage service. The following code creates a `cloud_table_client` object by using the storage account object you retrieved previously:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Create and add entities to a table

### <a name="create-a-table"></a>Skapa en tabell

A `cloud_table_client` object lets you get reference objects for tables and entities. The following code creates a `cloud_table_client` object and uses it to create a new table.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

To add an entity to a table, create a new `table_entity` object and pass it to `table_operation::insert_entity`. I följande kod används kundens förnamn som radnyckel och efternamnet som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Entities with the same partition key can be queried faster than entities with different partition keys. Using diverse partition keys allows for greater parallel operation scalability. Mer information finns i [checklistan för prestanda och skalbarhet i Microsoft Azure Storage](../storage/common/storage-performance-checklist.md).

The following code creates a new instance of `table_entity` with some customer data to store. The code next calls `table_operation::insert_entity` to create a `table_operation` object to insert an entity into a table, and associates the new table entity with it. Finally, the code calls the `execute` method on the `cloud_table` object. The new `table_operation` sends a request to the Table service to insert the new customer entity into the `people` table.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter

Du kan infoga en batch med entiteter i Table Storage i samma skrivåtgärd. The following code creates a `table_batch_operation` object, and then adds three insert operations to it. Each insert operation is added by creating a new entity object, setting its values, and then calling the `insert` method on the `table_batch_operation` object to associate the entity with a new insert operation. Then, the code calls `cloud_table.execute` to run the operation.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Några saker att tänka på när du använder batchåtgärder:

* You can do up to 100 `insert`, `delete`, `merge`, `replace`, `insert-or-merge`, and `insert-or-replace` operations in any combination in a single batch.  
* A batch operation can have a retrieve operation, if it's the only operation in the batch.  
* Alla entiteter i samma batchåtgärd måste ha samma partitionsnyckel.  
* Batchåtgärder är begränsade till en nyttolast på 4 MB.  

## <a name="query-and-modify-entities"></a>Query and modify entities

### <a name="retrieve-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition

To query a table for all entities in a partition, use a `table_query` object. The following code example specifies a filter for entities where `Smith` is the partition key. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.  

> [!NOTE]
> Dessa metoder stöds inte för närvarande för C++ i Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

The query in this example returns all the entities that match the filter criteria. Om du har stora tabeller och ofta behöver hämta tabellentiteterna rekommenderar vi att du lagrar dina data i Azure Storage-blobbar i stället.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Hämta ett intervall med enheter i en partition

If you don't want to query all the entities in a partition, you can specify a range. Combine the partition key filter with a row key filter. The following code example uses two filters to get all entities in partition `Smith` where the row key (first name) starts with a letter earlier than `E` in the alphabet, and then prints the query results.  

> [!NOTE]
> Dessa metoder stöds inte för närvarande för C++ i Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>Hämta en enda entitet

Du kan skriva en fråga för att hämta en enda, specifik entitet. The following code uses `table_operation::retrieve_entity` to specify the customer `Jeff Smith`. This method returns just one entity, rather than a collection, and the returned value is in `table_result`. Det snabbaste sättet att hämta en enskild entitet från tabelltjänsten är att ange både partitions- och radnycklar i en fråga.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>Ersätta en entitet

Om du vill ersätta en entitet hämtar du den från Table Storage, ändrar entitetsobjektet och sparar sedan ändringarna till Table Storage igen. Följande kod ändrar en befintlig kunds telefonnummer och e-postadress. Instead of calling `table_operation::insert_entity`, this code uses `table_operation::replace_entity`. This approach causes the entity to be fully replaced on the server, unless the entity on the server has changed since it was retrieved. If it has been changed, the operation fails. This failure prevents your application from overwriting a change made between the retrieval and update by another component. The proper handling of this failure is to retrieve the entity again, make your changes, if still valid, and then do another `table_operation::replace_entity` operation.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>Insert or replace an entity

`table_operation::replace_entity` operations fail if the entity has been changed since it was retrieved from the server. Furthermore, you must retrieve the entity from the server first in order for `table_operation::replace_entity` to be successful. Sometimes, you don't know if the entity exists on the server. The current values stored in it are irrelevant, because your update should overwrite them all. To accomplish this result, use a `table_operation::insert_or_replace_entity` operation. This operation inserts the entity if it doesn't exist. The operation replaces the entity if it exists. In the following code example, the customer entity for `Jeff Smith` is still retrieved, but it's then saved back to the server by using `table_operation::insert_or_replace_entity`. Eventuella entitetsuppdateringar som görs mellan hämtnings- och uppdateringsåtgärden skrivs över.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper

En fråga till en tabell kan bara hämta några få egenskaper från en entitet. The query in the following code uses the `table_query::set_select_columns` method to return only the email addresses of entities in the table.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Det är effektivare att köra frågor mot några få egenskaper från en entitet än att hämta alla egenskaper.
>

## <a name="delete-content"></a>Delete content

### <a name="delete-an-entity"></a>Ta bort en entitet

You can delete an entity after you retrieve it. After you retrieve an entity, call `table_operation::delete_entity` with the entity to delete. Then call the `cloud_table.execute` method. The following code retrieves and deletes an entity with a partition key of `Smith` and a row key of `Jeff`.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>Ta bort en tabell

I det sista kodexemplet tas en tabell bort från ett lagringskonto. A table that has been deleted is unavailable to be re-created for some time following the deletion.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>Felsöka

For Visual Studio Community Edition, if your project gets build errors because of the include files *storage_account.h* and *table.h*, remove the **/permissive-** compiler switch:

1. Högerklicka på projektet i **Solution Explorer** och välj **Egenskaper**.
1. Expandera **Konfigurationsegenskaper** i dialogrutan **Egenskapssidor**, expandera **C/C++** och välj **Språk**.
1. Ändra **Conformance mode** (Konformitetsläge) till **Nej**.

## <a name="next-steps"></a>Nästa steg

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.

Följ dessa länkar om du vill lära dig mer om Azure Storage och Azure Cosmos DB Table-API:et:

* [Introduktion till Table-API:et](table-introduction.md)
* [Lista över Azure Storage-resurser i C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Referens för Azure Storage-klientbiblioteket för C++](https://azure.github.io/azure-storage-cpp)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)
