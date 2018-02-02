---
title: "Hur du använder Azure Table storage med C++ | Microsoft Docs"
description: "Lagra strukturerade data i molnet med hjälp av Azure Table Storage, en NoSQL-databas."
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jahogg
editor: tysonn
ms.assetid: f191f308-e4b2-4de9-85cb-551b82b1ea7c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: a71098583af8722f2e191e0e665ac87ebd30f355
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-use-azure-table-storage-with-c"></a>Hur du använder Azure Table storage med C++
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av Azure Table storage-tjänsten. Exemplen är skrivna i C++ och Använd den [Azure Storage-klientbibliotek för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Scenarier som tas upp inkluderar **skapa och ta bort en tabell** och **arbeta med tabellentiteter**.

> [!NOTE]
> Den här handboken riktar sig mot Azure Storage-klientbibliotek för C++ version 1.0.0 och senare. Den rekommenderade versionen är Storage-klientbibliotek 2.2.0, som är tillgängliga via [NuGet](http://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program
I den här guiden använder lagringsfunktioner som kan köras i ett C++-program. Om du vill göra det, behöver du installera Azure Storage-klientbibliotek för C++ och skapa ett Azure storage-konto i din Azure-prenumeration.  

Om du vill installera Azure Storage-klientbibliotek för C++ kan du använda följande metoder:

* **Linux:** Följ instruktionerna på den [Azure Storage-klientbibliotek för C++ viktigt](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) sidan.  
* **Windows:** i Visual Studio klickar du på **Verktyg > NuGet Package Manager > Package Manager-konsolen**. Skriv följande kommando i den [NuGet Package Manager-konsolen](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) och tryck på RETUR.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-table-storage"></a>Konfigurera programmet att komma åt Table storage
Lägga till följande uttryck överst i filen C++ där du vill använda Azure storage API: er för att komma åt tabeller:  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Ställ in en anslutningssträng för Azure storage
Ett Azure storage-klienten använder en anslutningssträng för lagring för att lagra slutpunkter och autentiseringsuppgifter för åtkomst till data management services. Du måste ange anslutningssträngen för lagring i följande format när du kör ett klientprogram. Använd namnet på ditt lagringskonto eller åtkomstnyckel för lagring för lagringskontot som anges i den [Azure Portal](https://portal.azure.com) för den *AccountName* och *AccountKey* värden. Information om lagringskonton och snabbtangenterna finns [om Azure storage-konton](../storage/common/storage-create-storage-account.md). Det här exemplet visar hur du kan deklarera statiska fält att lagra anslutningssträngen:  

```cpp
// Define the connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Om du vill testa ditt program i den lokala Windows-baserade datorn du använder Azure [lagringsemulatorn](../storage/common/storage-use-emulator.md) som installeras med den [Azure SDK](https://azure.microsoft.com/downloads/). Storage-emulatorn är ett verktyg som simulerar Azure Blob, köer och tabellen tjänster som är tillgängliga på utvecklingsdatorn lokala. I följande exempel visas hur du kan deklarera statiska fält för anslutningssträngen till din lokala storage-emulatorn:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Starta Azure storage-emulatorn, klicka på den **starta** knappen eller tryck på Windows-tangenten. Börja skriva **Azure Storage-emulatorn**, och välj sedan **Microsoft Azure Storage-emulatorn** från listan med program.  

Följande exempel förutsätter att du har använt ett av dessa två sätt för att hämta anslutningssträngen för lagring.  

## <a name="retrieve-your-connection-string"></a>Hämta anslutningssträngen
Du kan använda den **cloud_storage_account** klass för att representera kontoinformationen lagring. Du kan använda parse-metod för att hämta information om ditt lagringskonto från anslutningssträngen för lagring.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Sedan hämta en referens till en **cloud_table_client** class, som gör det möjligt att hämta referensobjekt för tabeller och de entiteter som lagras i Table storage-tjänsten. Följande kod skapar en **cloud_table_client** objektet med hjälp av kontot lagringsobjektet vi hämta ovan:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>Skapa en tabell
En **cloud_table_client** objekt kan du få referensobjekt för tabeller och enheter. Följande kod skapar en **cloud_table_client** objekt och används för att skapa en ny tabell.

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
Om du vill lägga till en entitet i en tabell, skapa en ny **table_entity** objekt och skicka det till **table_operation::insert_entity**. Följande kod använder kundens förnamn som radnyckel och efternamn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Entiteter med samma partitionsnyckel kan frågas snabbare än de som har olika partitionsnycklar, men med olika partitionsnycklar möjliggör större parallell åtgärd skalbarhet. Mer information finns i [Microsoft Azure storage checklistan för prestanda och skalbarhet](../storage/common/storage-performance-checklist.md).

Följande kod skapar en ny instans av **table_entity** med vissa kundinformation lagras. Kod nästa anrop **table_operation::insert_entity** att skapa en **table_operation** objekt för att infoga en entitet i en tabell och associerar den nya tabellentiteten med den. Slutligen koden anropar metoden execute på den **cloud_table** objekt. Och de nya **table_operation** skickar en begäran till tabelltjänsten att lägga till nya kundentiteten i tabellen ”användare”.  

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
Du kan infoga en batch med entiteter till tabelltjänsten i samma skrivåtgärd. Följande kod skapar en **table_batch_operation** objekt och lägger sedan till tre infogningsåtgärder till den. Varje infogningen har lagts till genom att skapa ett nytt enhetsobjekt, dess inställningsvärden och anropar sedan metoden insert på den **table_batch_operation** objekt att associera entiteten med en ny insert-åtgärden. Sedan **cloud_table.execute** anropas för att utföra åtgärden.  

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

Några saker att tänka på batchåtgärder:  

* Du kan utföra upp till 100 insert-, delete, merge, Ersätt, insert-eller merge-och infoga eller ersätta i vilken kombination som helst i en enskild batch.  
* En batchåtgärd kan ha en hämta-åtgärd om det är den enda åtgärden i batchen.  
* Alla entiteter i samma batchåtgärd måste ha samma partitionsnyckel.  
* En batchåtgärd är begränsad till någon 4 MB nyttolast.  

## <a name="retrieve-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition
Om du vill fråga en tabell efter alla entiteter i en partition använder en **table_query** objekt. I följande kodexempel anges ett filter för entiteter där partitionsnyckeln är ”Smith”. Det här exemplet skriver ut fälten för varje entitet i frågeresultatet till konsolen.  

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

Frågan i det här exemplet ger alla entiteter som matchar filterkriterierna. Om du har stora tabeller och behöver för att hämta tabellentiteter ofta, rekommenderar vi att lagrar du data i Azure storage-blobbar i stället.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Hämta ett intervall med enheter i en partition
Om du inte vill fråga efter alla entiteter i en partition kan du ange ett intervall genom att kombinera partitionsnyckelfiltret med ett radnyckelfilter. I följande kodexempel används två filter för att hämta alla entiteter i partitionen ”Smith” där radnyckeln (förnamn) börjar med en bokstav som kommer före ”E” i alfabetet, varefter frågeresultatet skrivs ut.  

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
Du kan skriva en fråga för att hämta en enda, specifik entitet. I följande kod används **table_operation::retrieve_entity** att ange kunden Jeff Smith. Den här metoden returnerar endast en entitet i stället för en samling och värdet som returneras i **table_result**. Det snabbaste sättet att hämta en enskild entitet från tabelltjänsten är att ange både partitions- och radnycklar i en fråga.  

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
Om du vill ersätta en entitet hämtar du den från tabelltjänsten, ändrar entitetsobjektet och sparar sedan ändringarna till tabelltjänsten. Följande kod ändrar en befintlig kunds telefonnummer och e-postadress. Istället för att ringa **table_operation::insert_entity**använder den här koden **table_operation::replace_entity**. Detta leder till att entiteten ersätts helt på servern, såvida inte entiteten på servern har ändrats sedan den hämtades. I så fall misslyckas åtgärden. Det här felet är avsett att förhindra att ditt program oavsiktligt skriver över ändringar mellan hämtningen och uppdateringen av en annan komponent i ditt program. Att hantera det här felet är att hämta entiteten igen, gör ändringarna (om det är fortfarande giltigt) och sedan köra en **table_operation::replace_entity** igen. I nästa avsnitt visar vi hur du kan åsidosätta detta beteende.  

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
**table_operation::replace_entity** åtgärder misslyckas om entiteten har ändrats sedan den hämtades från servern. Dessutom måste du hämta entiteten från servern först för att **table_operation::replace_entity** ska lyckas. Ibland, men du inte vet om entiteten finns på servern och de aktuella värden som lagras i den är irrelevanta – dina uppdateringar ska skriva över alla. För att åstadkomma detta använder du en **table_operation::insert_or_replace_entity** igen. Den här åtgärden infogar entiteten om den inte finns, eller ersätter den om den finns, oavsett när den senaste uppdateringen gjordes. I följande kodexempel hämtas fortfarande kundentiteten för Jeff Smith, men den sparas sedan tillbaka till servern via **table_operation::insert_or_replace_entity**. Uppdateringar som görs i entiteten mellan hämtnings- och update-åtgärden skrivs över.  

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
En fråga till en tabell kan hämta bara några få egenskaper från en entitet. Frågan i följande kod använder den **table_query::set_select_columns** metod för att returnera e-postadresserna för entiteter i tabellen.  

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
> Frågar några egenskaper från en entitet är effektivare än att hämta alla egenskaper.
> 
> 

## <a name="delete-an-entity"></a>Ta bort en entitet
Du kan enkelt ta bort en enhet när du har hämtat den. När entiteten hämtas anropa **table_operation::delete_entity** med att ta bort entiteten. Sedan anropar den **cloud_table.execute** metod. Följande kod hämtar och tar bort en entitet med en partitionsnyckel för ”Smith” och en radnyckel av ”Jeff”.  

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

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i table storage kan du följa dessa länkar om du vill veta mer om Azure Storage:  

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Hur du använder Blob storage från C++](../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Använda Queue storage från C++](../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Visa en lista med Azure Storage-resurser i C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Storage-klientbibliotek för C++-referens](http://azure.github.io/azure-storage-cpp)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)
