---
title: Använda Azure Table Storage och Azure Cosmos DB Table API med C++
description: Lagra strukturerade data i molnet med Azure Table Storage eller Azure Cosmos DB Table API.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: e6d61e329ba91f53b11ace4d258b35950e188dcb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76771222"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Använda Azure Table Storage och Azure Cosmos DB Table-API:et med C++

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Den här guiden visar vanliga scenarier med hjälp av Azure Table Storage Service eller Azure Cosmos DB Table API. Exemplen är skrivna i C++ och använder [Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Den här artikeln innehåller följande scenarier:

* Skapa och ta bort en tabell
* Arbeta med tabellentiteter

> [!NOTE]
> För den här guiden krävs Azure Storage-klientbiblioteket för C++ version 1.0.0 eller senare. Den rekommenderade versionen är Storage Client Library 2.2.0, som är tillgängligt med [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Skapa konton

### <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett Azure Cosmos DB Table API-konto

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program

I den här guiden använder du lagringsfunktioner från ett C++-program. Det gör du genom att installera Azure Storage Client Library för C++.

Så här installerar du Azure Storage Client Library for C++:

* **Linux:** Följ instruktionerna i [Azure Storage Client Library for C++ README: Komma igång på Linux-sidan.](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)
* **Windows:** I Windows använder du [vcpkg](https://github.com/microsoft/vcpkg) som beroendehanterare. Följ [snabbstarten](https://github.com/microsoft/vcpkg#quick-start) för att initiera vcpkg. Använd sedan följande kommando för att installera biblioteket:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Du kan hitta en guide för hur du skapar källkoden och exporterar till Nuget i [README-filen.](https://github.com/Azure/azure-storage-cpp#download--install)

### <a name="configure-access-to-the-table-client-library"></a>Konfigurera åtkomst till klientbiblioteket för Table Storage

Om du vill använda Azure storage API:er för att komma åt tabeller lägger du till följande `include` satser högst upp i C++-filen:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Azure Storage- och Cosmos DB-klienter använder en anslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst till datahanteringstjänster. När du kör ett klientprogram måste du ange lagringsanslutningssträngen eller Azure Cosmos DB-anslutningssträngen i lämpligt format.

### <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en Azure Storage-anslutningssträng

I det här exemplet visas hur du deklarerar ett statiskt fält för att hålla azure storage-anslutningssträngen:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Använd namnet på ditt `<your_storage_account>`lagringskonto för . För <your_storage_account_key> använder du åtkomstnyckeln för lagringskontot som visas i [Azure-portalen](https://portal.azure.com). Information om lagringskonton och åtkomstnycklar finns i [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Konfigurera en Azure Cosmos DB-anslutningssträng

I det här exemplet visas hur du deklarerar ett statiskt fält för att hålla Azure Cosmos DB-anslutningssträngen:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Använd namnet på ditt Azure Cosmos DB-konto för `<your_cosmos_db_account>`. Ange primärnyckeln `<your_cosmos_db_account_key>`för . Ange slutpunkten som visas i `<your_cosmos_db_endpoint>` [Azure-portalen](https://portal.azure.com) för .

Om du vill testa ditt program på den lokala Windows-baserade datorn kan du använda Azure Storage-emulatorn som installeras med [Azure SDK](https://azure.microsoft.com/downloads/). Azure Storage-emulatorn är ett verktyg som simulerar tjänsterna Azure Blob, Queue och Table som finns på den lokala utvecklingsdatorn. I följande exempel visas hur du deklarerar ett statiskt fält för att hålla anslutningssträngen till din lokala lagringsemulator:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Om du vill starta Azure-lagringsemulatorn väljer du **Start-knappen** eller Windows-tangenten från skrivbordet i Windows. Ange och kör *Microsoft Azure Storage Emulator*. Mer information finns i [Använda Azure-lagringsemulatorn för utveckling och testning](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Hämta anslutningssträngen

Du kan `cloud_storage_account` använda klassen för att representera din lagringskontoinformation. Om du vill hämta information om lagringskontot från lagringsanslutningssträngen `parse` använder du metoden.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Hämta sedan en referens `cloud_table_client` till en klass. Med den här klassen kan du hämta referensobjekt för tabeller och entiteter som lagras i table storage-tjänsten. Följande kod skapar `cloud_table_client` ett objekt med hjälp av det lagringskontoobjekt som du hämtade tidigare:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Skapa och lägga till entiteter i en tabell

### <a name="create-a-table"></a>Skapa en tabell

Med `cloud_table_client` ett objekt kan du hämta referensobjekt för tabeller och entiteter. Följande kod skapar `cloud_table_client` ett objekt och använder det för att skapa en ny tabell.

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

Om du vill lägga till en `table_entity` entitet `table_operation::insert_entity`i en tabell skapar du ett nytt objekt och skickar det till . I följande kod används kundens förnamn som radnyckel och efternamnet som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Entiteter med samma partitionsnyckel kan efterfrågas snabbare än entiteter med olika partitionsnycklar. Genom att använda olika partitionsnycklar kan du skala parallella åtgärder. Mer information finns i [checklistan för prestanda och skalbarhet i Microsoft Azure Storage](../storage/common/storage-performance-checklist.md).

Följande kod skapar en ny `table_entity` instans av med vissa kunddata att lagra. Koden anropas `table_operation::insert_entity` nästa `table_operation` gång för att skapa ett objekt för att infoga en entitet i en tabell och associerar den nya tabellentiteten med den. Slutligen anropar koden `execute` metoden `cloud_table` på objektet. Den `table_operation` nya skickar en begäran till tabelltjänsten om `people` du vill infoga den nya kundentiteten i tabellen.  

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

Du kan infoga en batch med entiteter i Table Storage i samma skrivåtgärd. Följande kod skapar `table_batch_operation` ett objekt och lägger sedan till tre infoga åtgärder i det. Varje infogningsåtgärd läggs till genom att skapa ett `insert` nytt entitetsobjekt, ange dess värden och sedan anropa metoden på `table_batch_operation` objektet för att associera entiteten med en ny infogningsåtgärd. Sedan kodanrop `cloud_table.execute` för att köra åtgärden.  

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

* Du kan göra upp till `insert` `delete`100 , , `merge`, `replace` `insert-or-merge`och `insert-or-replace` operationer i valfri kombination i en enskild batch.  
* En batchåtgärd kan ha en hämtningsåtgärd om det är den enda åtgärden i batchen.  
* Alla entiteter i samma batchåtgärd måste ha samma partitionsnyckel.  
* Batchåtgärder är begränsade till en nyttolast på 4 MB.  

## <a name="query-and-modify-entities"></a>Fråga och ändra entiteter

### <a name="retrieve-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition

Om du vill fråga en tabell för `table_query` alla entiteter i en partition använder du ett objekt. I följande kodexempel anges ett `Smith` filter för entiteter där partitionsnyckeln är. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.  

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

Frågan i det här exemplet returnerar alla entiteter som matchar filtervillkoren. Om du har stora tabeller och ofta behöver hämta tabellentiteterna rekommenderar vi att du lagrar dina data i Azure Storage-blobbar i stället.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Hämta ett intervall med enheter i en partition

Om du inte vill fråga alla entiteter i en partition kan du ange ett intervall. Kombinera partitionsnyckelfiltret med ett radnyckelfilter. I följande kodexempel används två filter `Smith` för att hämta alla entiteter `E` i partition där radnyckeln (förnamn) börjar med en bokstav tidigare än i alfabetet och sedan utskrifter frågeresultaten.  

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

Du kan skriva en fråga för att hämta en enda, specifik entitet. Följande kod `table_operation::retrieve_entity` används för `Jeff Smith`att ange kunden . Den här metoden returnerar bara en entitet i `table_result`stället för en samling, och det returnerade värdet finns i . Det snabbaste sättet att hämta en enskild entitet från tabelltjänsten är att ange både partitions- och radnycklar i en fråga.  

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

Om du vill ersätta en entitet hämtar du den från Table Storage, ändrar entitetsobjektet och sparar sedan ändringarna till Table Storage igen. Följande kod ändrar en befintlig kunds telefonnummer och e-postadress. I stället `table_operation::insert_entity`för att `table_operation::replace_entity`anropa används den här koden . Den här metoden medför att entiteten ersätts fullständigt på servern, såvida inte entiteten på servern har ändrats sedan den hämtades. Om den har ändrats misslyckas åtgärden. Det här felet förhindrar att programmet skriver över en ändring som gjorts mellan hämtningen och uppdateringen av en annan komponent. En korrekt hantering av det här felet är att hämta entiteten igen, göra ändringarna, om de fortfarande är giltiga, och sedan utföra en annan `table_operation::replace_entity` åtgärd.  

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

### <a name="insert-or-replace-an-entity"></a>Infoga eller ersätta en entitet

`table_operation::replace_entity`åtgärder misslyckas om entiteten har ändrats sedan den hämtades från servern. Dessutom måste du hämta entiteten från servern `table_operation::replace_entity` först för att lyckas. Ibland vet du inte om entiteten finns på servern. De aktuella värden som lagras i den är irrelevanta, eftersom uppdateringen bör skriva över dem alla. Använd en `table_operation::insert_or_replace_entity` åtgärd för att uppnå detta resultat. Den här åtgärden infogar entiteten om den inte finns. Åtgärden ersätter entiteten om den finns. I följande kodexempel hämtas kundentiteten för `Jeff Smith` fortfarande, men den sparas sedan tillbaka till servern med hjälp `table_operation::insert_or_replace_entity`av . Eventuella entitetsuppdateringar som görs mellan hämtnings- och uppdateringsåtgärden skrivs över.  

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

En fråga till en tabell kan bara hämta några få egenskaper från en entitet. Frågan i följande kod `table_query::set_select_columns` använder metoden för att returnera endast e-postadresserna till entiteter i tabellen.  

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

## <a name="delete-content"></a>Ta bort innehåll

### <a name="delete-an-entity"></a>Ta bort en entitet

Du kan ta bort en entitet när du har hämtat den. När du har hämtat en entitet anropar du `table_operation::delete_entity` entiteten som ska tas bort. Anropa sedan `cloud_table.execute` metoden. Följande kod hämtar och tar bort en entitet med en partitionsnyckel `Smith` och en radnyckel `Jeff`i .

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

I det sista kodexemplet tas en tabell bort från ett lagringskonto. En tabell som har tagits bort kan inte återskapas under en viss tid efter borttagningen.  

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

## <a name="troubleshooting"></a>Felsökning

Om projektet får byggfel på grund av inklumerade filer *storage_account.h* och *table.h*för Visual Studio Community Edition tar du bort växeln **/tillåtande** kompilator:

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
