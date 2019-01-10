---
title: Så använder du Azure Table Storage och Azure Cosmos DB Table API med C++
description: Lagra strukturerade data i molnet med Azure Table Storage eller Table-API:et för Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: 40b84a56f93ad670a26eb876a18820e0d4037f63
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033986"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Använda Azure Table Storage och Azure Cosmos DB Table-API:et med C++
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Översikt
Den här guiden beskriver hur du hanterar vanliga scenarier med hjälp av tjänsten Azure Table Storage eller Azure Cosmos DB Table-API:et. Exemplen är skrivna i C++ och använder [Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Scenarierna som vi går igenom beskriver bland annat hur du **skapar och tar bort en tabell** och hur du **arbetar med tabellentiteter**.

> [!NOTE]
> För den här guiden krävs Azure Storage-klientbiblioteket för C++ version 1.0.0 eller senare. Den rekommenderade versionen är Storage-klientbibliotek 2.2.0, som är tillgängligt via [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 

## <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett konto för Table-API:et för Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program
I den här guiden ska du använda lagringsfunktioner som kan köras i ett C++-program. För att göra det måste du installera Azure Storage-klientbiblioteket för C++ och skapa ett Azure Storage-konto i din Azure-prenumeration.  

Du kan installera Azure Storage-klientbiblioteket för C++ med någon av följande metoder:

* **Linux:** Följ instruktionerna på [sidan med viktig information om Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
* **Windows:** Klicka på **Verktyg > NuGet Package Manager > Package Manager Console** i Visual Studio. Skriv följande kommando i [NuGet Package Manager-konsolen](/nuget/tools/package-manager-console) och tryck på Retur.  
  
     Install-Package wastorage

## <a name="configure-access-to-the-table-client-library"></a>Konfigurera åtkomst till klientbiblioteket för Table Storage
Lägg till följande include-instruktioner överst i C++-filen där du vill använda Azure Storage-API:erna för att komma åt tabeller:  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Azure Storage- och Cosmos DB-klienter använder en anslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst till datahanteringstjänster. När du kör ett klientprogram måste du ange Azure Storage-anslutningssträngen eller Azure Cosmos DB-anslutningssträngen i rätt format.

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en Azure Storage-anslutningssträng
 Använd namnet på ditt Azure Storage-konto och åtkomstnyckeln för lagringskontot, dvs. värdena för *AccountName* och *AccountKey* på [Azure Portal](https://portal.azure.com). Information om lagringskonton och snabbtangenter finns i avsnittet [Om Azure Storage-konton](../storage/common/storage-create-storage-account.md). Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av Azure Storage-anslutningssträngen:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="set-up-an-azure-cosmos-db-connection-string"></a>Konfigurera en Azure Cosmos DB-anslutningssträng
Använd namnet på ditt Azure Cosmos DB-konto, primärnyckeln och slutpunkten, dvs. värdena för *AccountName*, *PrimaryKey* och  *Endpoint* på [Azure Portal](https://portal.azure.com). Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av Azure Cosmos DB-anslutningssträngen:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_cosmos_db_account;AccountKey=your_cosmos_db_account_key;TableEndpoint=your_cosmos_db_endpoint"));
```


Om du vill testa ditt program på den lokala Windows-baserade datorn kan du använda Azure [Storage-emulatorn](../storage/common/storage-use-emulator.md) som installeras med [Azure SDK](https://azure.microsoft.com/downloads/). Azure Storage-emulatorn är ett verktyg som simulerar tjänsterna Azure Blob, Queue och Table som finns på den lokala utvecklingsdatorn. Följande exempel visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen i den lokala lagringsemulatorn:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Du startar Azure Storage-emulatorn genom att först klicka på **Start** eller trycka på Windows-tangenten. Börja skriva **Azure Storage-emulator** och välj sedan **Microsoft Azure Storage-emulator** i listan med program.  

Följande exempel förutsätter att du har använt någon av dessa två metoder för att hämta Azure Storage-anslutningssträngen.  

## <a name="retrieve-your-connection-string"></a>Hämta anslutningssträngen
Du kan använda klassen **cloud_storage_account** för att representera informationen om lagringskontot. Du hämtar informationen om lagringskontot från Azure Storage-anslutningssträngen med hjälp av metoden **parse**.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Därefter hämtar du en referens till en **cloud_table_client**-klass. På så sätt kan du hämta referensobjekt för tabeller och entiteter som lagras i Table Storage-tjänsten. Följande kod skapar ett **cloud_table_client**-objekt med hjälp av objektet för lagringskontot som vi hämtade ovan:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>Skapa en tabell
Med ett **cloud_table_client**-objekt kan du hämta referensobjekt för tabeller och enheter. Följande kod skapar ett **cloud_table_client**-objekt och använder det för att skapa en ny tabell.

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

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Om du vill lägga till en entitet i en tabell skapar du ett nytt **table_entity**-objekt och skickar det till **table_operation::insert_entity**. I följande kod används kundens förnamn som radnyckel och efternamnet som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Det går snabbare att fråga entiteter med samma partitionsnyckel än entiteter som har olika partitionsnycklar, men möjligheten att utföra parallella åtgärder med hög skalbarhet är större med olika partitionsnycklar. Mer information finns i [checklistan för prestanda och skalbarhet i Microsoft Azure Storage](../storage/common/storage-performance-checklist.md).

I följande kod skapas en ny instans av **table_entity** med en del kundinformation som ska lagras. Koden anropar sedan **table_operation::insert_entity** för att skapa ett **table_operation**-objekt som infogar en entitet i en tabell, och associerar sedan den nya tabellentiteten med den. Slutligen anropar koden metoden execute för **cloud_table**-objektet. Det nya **table_operation**-objektet skickar sedan en begäran till Table Storage och uppmanar tjänsten att infoga den nya kundentiteten i tabellen ”people”.  

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

## <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter
Du kan infoga en batch med entiteter i Table Storage i samma skrivåtgärd. Följande kod skapar ett **table_batch_operation**-objekt och lägger sedan till tre insert-åtgärder i objektet. Koden lägger till varje insert-åtgärd genom att skapa en ny entitet, ange dess värden och sedan anropa insert-metoden för **table_batch_operation**-objektet för att associera entiteten med en ny insert-åtgärd. Därefter anropas **cloud_table.execute** för att köra åtgärden.  

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

* Du kan utföra upp till 100 insert-, delete-, merge-, replace-, insert-or-merge- och insert-or-replace-åtgärder i valfri kombination i samma batch.  
* En batchåtgärd kan innehålla en retrieve-åtgärd om det är den enda åtgärden i batchen.  
* Alla entiteter i samma batchåtgärd måste ha samma partitionsnyckel.  
* Batchåtgärder är begränsade till en nyttolast på 4 MB.  

## <a name="retrieve-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition
Om du vill fråga en tabell efter alla entiteter i en partition använder du ett **table_query**-objekt. I följande kodexempel anges ett filter för entiteter där partitionsnyckeln är ”Smith”. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.  

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

Frågan i det här exemplet hämtar alla entiteter som matchar filterkriterierna. Om du har stora tabeller och ofta behöver hämta tabellentiteterna rekommenderar vi att du lagrar dina data i Azure Storage-blobbar i stället.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Hämta ett intervall med enheter i en partition
Om du inte vill fråga efter alla entiteter i en partition kan du ange ett intervall genom att kombinera partitionsnyckelfiltret med ett radnyckelfilter. I följande kodexempel används två filter för att hämta alla entiteter i partitionen ”Smith” där radnyckeln (förnamn) börjar med en bokstav som kommer före ”E” i alfabetet, varefter frågeresultatet skrivs ut.  

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

## <a name="retrieve-a-single-entity"></a>Hämta en enda entitet
Du kan skriva en fråga för att hämta en enda, specifik entitet. I följande kod används **table_operation::retrieve_entity** för att ange kunden ”Jeff Smith”. Den här metoden returnerar endast en entitet i stället för en samling, och värdet som returneras finns i **table_result**. Det snabbaste sättet att hämta en enskild entitet från tabelltjänsten är att ange både partitions- och radnycklar i en fråga.  

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

## <a name="replace-an-entity"></a>Ersätta en entitet
Om du vill ersätta en entitet hämtar du den från Table Storage, ändrar entitetsobjektet och sparar sedan ändringarna till Table Storage igen. Följande kod ändrar en befintlig kunds telefonnummer och e-postadress. I stället för att anropa **table_operation::insert_entity** används **table_operation::replace_entity** i den här koden. Detta leder till att entiteten ersätts helt på servern, såvida inte entiteten på servern har ändrats sedan den hämtades. I så fall misslyckas åtgärden. Det här felet är avsett att förhindra att ditt program oavsiktligt skriver över ändringar mellan hämtningen och uppdateringen av en annan komponent i ditt program. Du åtgärdar det här felet genom att hämta entiteten igen, göra dina ändringar (om de fortfarande behövs) och sedan köra en **table_operation::replace_entity**-åtgärd igen. I nästa avsnitt visar vi hur du kan åsidosätta detta beteende.  

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

## <a name="insert-or-replace-an-entity"></a>Infoga eller ersätta en entitet
**table_operation::replace_entity**-åtgärder misslyckas om entiteten har ändrats sedan den hämtades från servern. Du måste dessutom hämta entiteten från servern först för att **table_operation::replace_entity**-åtgärden ska lyckas. Ibland vet du dock inte om entiteten finns på servern och om de aktuella värdena som lagras i den är irrelevanta – uppdateringen bör skriva över samtliga. För att göra det använder du en **table_operation::insert_or_replace_entity**-åtgärd. Den här åtgärden infogar entiteten om den inte finns, eller ersätter den om den finns, oavsett när den senaste uppdateringen gjordes. I följande kodexempel hämtas fortfarande kundentiteten för Jeff Smith, men den sparas till servern igen via **table_operation::insert_or_replace_entity**. Eventuella entitetsuppdateringar som görs mellan hämtnings- och uppdateringsåtgärden skrivs över.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert-or-replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert-or-replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

## <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper
En fråga till en tabell kan hämta några få egenskaper från en entitet. Frågan i följande kod använder metoden **table_query::set_select_columns** för att endast returnera e-postadresserna för entiteter i tabellen.  

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
> 

## <a name="delete-an-entity"></a>Ta bort en entitet
Du kan enkelt ta bort en entitet när du har hämtat den. När entiteten har hämtats anropar du **table_operation::delete_entity** med entiteten som ska tas bort. Anropa sedan metoden **cloud_table.execute**. Följande kod hämtar och tar bort en entitet med partitionsnyckeln ”Smith” och radnyckeln ”Jeff”.  

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

## <a name="delete-a-table"></a>Ta bort en tabell
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
* Kompileringsfel i Visual Studio 2017 Community Edition

  Om kompileringsfel returneras för ditt projekt på grund av include-filerna storage_account.h och table.h, tar du bort kompileringsväxeln **/permissive-**. 
  - Högerklicka på projektet i **Solution Explorer** och välj **Egenskaper**.
  - Expandera **Konfigurationsegenskaper** i dialogrutan **Egenskapssidor**, expandera **C/C++** och välj **Språk**.
  - Ändra **Conformance mode** (Konformitetsläge) till **Nej**.
   
## <a name="next-steps"></a>Nästa steg
Följ dessa länkar om du vill lära dig mer om Azure Storage och Azure Cosmos DB Table-API:et: 

* [Introduktion till Table-API:et](table-introduction.md)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Lista över Azure Storage-resurser i C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Referens för Azure Storage-klientbiblioteket för C++](https://azure.github.io/azure-storage-cpp)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)
