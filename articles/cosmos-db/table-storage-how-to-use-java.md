---
title: Så använder du Azure Table Storage eller tabell-API för Azure Cosmos DB från Java
description: Lagra strukturerade data i molnet med Azure Table Storage eller Table-API:et för Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: d7be0ed8d59063f75b3d4fadbf69237bdb2d0d13
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863694"
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Så använder du Azure Table Storage eller Azure Cosmos DB Table API från Java
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Översikt
I den här artikeln visas hur du utför vanliga scenarier med hjälp av tjänsten Azure Table Storage och Azure Cosmos DB. Exemplen är skrivna i Java och använder [Azure Storage SDK för Java][Azure Storage SDK for Java]. De scenarier som behandlas beskriver hur du **skapar**, **listar** och **tar bort** tabeller samt hur du **infogar**, **kör frågor mot**, **modifierar** och **tar bort** poster i en tabell. Mer information om tabeller finns i avsnittet [Nästa steg](#next-steps).

> [!NOTE]
> En SDK finns tillgänglig för utvecklare som använder Azure Storage på Android-enheter. Mer information finns i [Azure Storage SDK för Android][Azure Storage SDK for Android].
>

## <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Skapa ett Java-program
I den här guiden använder du lagringsfunktioner som du kan köra i ett Java-program lokalt eller i kod som körs i en webbroll eller i en arbetsroll i Azure.

För att använda exemplen i den här artikeln installerar du Java Development Kit (JDK) och skapar sedan ett Azure Storage-konto eller ett Azure Cosmos DB-konto i din Azure-prenumeration. När du har gjort det verifierar du att utvecklingsdatorn uppfyller de minimikrav och de beroenden som anges i lagringsplatsen för [Azure Storage SDK för Java][Azure Storage SDK for Java] på GitHub. Om datorn uppfyller kraven kan du följa instruktionerna för att ladda ned och installera Azure Storage-biblioteken för Java på datorn från lagringsplatsen. När du har slutfört de uppgifterna kan du skapa ett Java-program som använder exemplen i den här artikeln.

## <a name="configure-your-application-to-access-table-storage"></a>Konfigurera programmet för att använda Table-lagring
Lägg till följande importinstruktioner överst i Java-filen där du vill använda Azure Storage-API:er eller Azure Cosmos DB Table API för att komma åt tabeller:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-an-azure-storage-connection-string"></a>Lägga till en Azure Storage-anslutningssträng
En Azure Storage-klient använder en förvaringsanslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst av datahanteringstjänster. När den körs i ett klientprogram måste du ange anslutningssträngen för lagring i följande format, med namnet på ditt lagringskonto och den primära åtkomstnyckel för lagringskontot som anges i [Azure-portalen](https://portal.azure.com) för värdena *AccountName* och *AccountKey*. 

Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

## <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Lägga till en Azure Cosmos DB Table API-anslutningssträng
Ett Azure Cosmos DB-konto använder en anslutningssträng för att lagra tabellslutpunkten och dina autentiseringsuppgifter. När den körs i ett klientprogram måste du ange Azure Cosmos DB-anslutningssträngen i följande format, med namnet på ditt Azure Cosmos DB-konto och den primära åtkomstnyckel för kontot som anges i [Azure-portalen](https://portal.azure.com) för värdena *AccountName* och *AccountKey*. 

Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av Azure Cosmos DB-anslutningssträngen:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

I ett program som körs inuti en roll i Azure kan du lagra den här strängen i tjänstkonfigurationsfilen *ServiceConfiguration.cscfg*, och du kan komma åt den med ett anrop till metoden  **RoleEnvironment.getConfigurationSettings**. Här är ett exempel på hur anslutningssträngen från ett **inställningselement** med namnet *StorageConnectionString* i tjänstkonfigurationsfilen kan hämtas:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Du kan även lagra anslutningssträngen i projektets config.properties-fil:

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

Följande exempel förutsätter att du har använt någon av dessa metoder för att hämta anslutningssträngen för lagring.

## <a name="create-a-table"></a>Skapa en tabell
Med ett **CloudTableClient**-objekt kan du hämta referensobjekt för tabeller och entiteter. Följande kod skapar ett **CloudTableClient**-objekt och använder det för att skapa ett nytt **CloudTable**-objekt som representerar en tabell med namnet ”people” (personer). 

> [!NOTE]
> Det finns andra sätt att skapa **CloudStorageAccount**-objekt; mer information finns i **CloudStorageAccount** i [Azure Storage Client SDK-referens]).
>

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>Lista tabellerna
Om du vill få en lista över tabeller anropar du metoden **CloudTableClient.listTables()** för att hämta en itererbar lista över tabellnamn.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Entiteter mappar till Java-objekt med hjälp av en anpassad klass som implementerar **TableEntity**. För enkelhetens skull implementerar klassen **TableServiceEntity** **TableEntity** och använder reflektion för att mappa egenskaper till getter- och setter-metoder som namngivits efter egenskaperna. Om du vill lägga till en entitet i en tabell skapar du först en klass som definierar egenskaperna för entiteten. Följande kod definierar en entitetsklass som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Entiteter med samma partitionsnyckel kan frågas snabbare än dem som har olika partitionsnycklar.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

Tabellåtgärder som rör entiteter kräver ett **TableOperation**-objekt. Det här objektet definierar den åtgärd som ska utföras på en entitet, vilket kan utföras med ett **CloudTable**-objekt. Med följande kod skapas en ny instans av klassen **CustomerEntity** med en del kundinformation som ska lagras. Koden anropar sedan **TableOperation.insertOrReplace** för att skapa ett **TableOperation**-objekt som infogar en entitet i en tabell, och associerar den nya **CustomerEntity** med den. Slutligen anropar koden **execute**-metoden i **CloudTable**-objektet och anger tabellen ”people” (personer) och den nya **TableOperation**, som sedan skickar en begäran till lagringstjänsten för att infoga den ny kundentiteten i tabellen ”people” (personer) eller ersätta entiteten om den redan finns.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>Infoga en batch med entiteter
Du kan skriva en batch med entiteter till Table Service i samma skrivåtgärd. Följande kod skapar ett **TableBatchOperation**-objekt och lägger sedan till tre insert-åtgärder till det. Koden lägger till varje insert-åtgärd genom att skapa en ny entitet, ange dess värden och sedan anropa **insert**-metoden för **TableBatchOperation**-objektet för att associera entiteten med en ny insert-åtgärd. Koden anropar sedan **execute** på **CloudTable**-objektet och anger tabellen ”people” (personer) och **TableBatchOperation**-objektet, som skickar batchen med tabellåtgärder till lagringstjänsten i en enskild begäran.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Några saker att tänka på när du använder batchåtgärder:

* Du kan utföra upp till 100 insert-, delete-, merge-, replace-, insert-or-merge- samt insert-or-replace-åtgärder i valfri kombination i samma batch.
* En batchåtgärd kan innehålla en retrieve-åtgärd om det är den enda åtgärden i batchen.
* Alla entiteter i samma batchåtgärd måste ha samma partitionsnyckel.
* Batchåtgärder är begränsade till en nyttolast på 4 MB.

## <a name="retrieve-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition
Om du vill fråga en tabell efter entiteter i en partition kan du använda en **TableQuery**. Anropa **TableQuery.from** för att skapa en fråga på en viss tabell som returnerar en angiven resultattyp. I följande kod anges ett filter för entiteter där partitionsnyckeln är ”Smith”. **TableQuery.generateFilterCondition** är en hjälpmetod för att skapa filter för frågor. Anropa **where** på den referens som returneras av metoden **TableQuery.from** för att tillämpa filtret på frågan. När frågan körs med ett anrop till **execute** på **CloudTable**-objektet returnerar den en **Iterator** med resultattypen **CustomerEntity** angiven. Du kan sedan använda den **Iterator** som returneras i en for each-loop för att förbruka resultaten. Med den här koden skrivs fälten för varje entitet i frågeresultatet ut till konsolen.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Hämta ett intervall med enheter i en partition
Om du inte vill fråga efter alla entiteter i en partition kan du ange ett intervall genom att använda jämförelseoperatorer i ett filter. I följande kod kombineras två filter för att hämta alla entiteter i partitionen ”Smith” där radnyckeln (förnamn) börjar med en bokstav fram till ”E” i alfabetet. Sedan skrivs frågeresultatet ut. Om du använder de enheter som lagts till i tabellen i batch insert-avsnittet i den här guiden kan bara två entiteter returneras den här gången (Ben och Denise Smith). Jeff Smith inkluderas inte.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>Hämta en enda entitet
Du kan skriva en fråga för att hämta en enda, specifik entitet. I följande kod anropas **TableOperation.retrieve** med partitionsnyckel- och radnyckelparametrar för att ange kunden ”Jeff Smith” i stället för att skapa en **TableQuery** och använda filter för att göra samma sak. Vid exekvering returnerar hämtningsåtgärden endast en entitet i stället för en samling. Metoden **getResultAsType** omvandlar resultatet till tilldelningsmålets typ, ett **CustomerEntity**-objekt. Om den här typen inte är kompatibel med den typ som angetts för frågan utlöses ett undantag. Ett null-värde returneras om ingen entitet har en exakt partitions- och radnyckelmatchning. Det snabbaste sättet att hämta en enskild entitet från tabelltjänsten är att ange både partitions- och radnycklar i en fråga.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>Ändra en entitet
Om du vill ändra en entitet hämtar du den från Table Storage, ändrar entitetsobjektet och sparar ändringarna till Table Storage igen med en replace- eller en merge-åtgärd. Följande kod ändrar en befintlig kunds telefonnummer. I stället för att anropa **TableOperation.insert**, som vi gjorde för att infoga, anropar den här koden **TableOperation.replace**. Metoden **CloudTable.execute** anropar Table Service, och entiteten ersätts såvida inte ett annat program ändrade den under den tid sedan det här programmet hämtade den. När det utlöses ett undantag och entiteten måste hämtas, ändras och sparas igen. Detta optimistiska återförsöksmönster för samtidighet är vanligt i ett distribuerat lagringssystem.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper
En fråga till en tabell kan hämta några få egenskaper från en entitet. Den här tekniken, kallad projektion, minskar bandbredden och kan förbättra frågeprestanda, i synnerhet för stora entiteter. Frågan i följande kod använder metoden **select** för att bara returnera e-postadresserna för entiteter i tabellen. Resultatet projiceras till en samling av **String** med hjälp av en **EntityResolver**, som utför typkonvertering på de entiteter som returneras från servern. Du kan lära dig mer om projektion i [Azure Tables: Introducing Upsert and Query Projection][Azure Tables: Introducing Upsert and Query Projection] (Azure Tables: introduktion av Upsert och Query Projection). Observera att projektion inte stöds i den lokala lagringsemulatorn. Det betyder att den här koden endast körs vid användning av ett konto i Table Service.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define a Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>Infoga eller ersätta en entitet
Ofta finns det ett behov av att lägga till en entitet i en tabell utan kännedom om huruvida den redan finns i tabellen. Med en insert-or-replace-åtgärd kan du göra en enskild begäran som infogar entiteten om den inte redan finns eller ersätter den befintliga om den finns. Följande kod bygger på föregående exempel och infogar eller ersätter entiteten för ”Walter Harp”. När du har skapat en ny entitet anropar koden metoden **TableOperation.insertOrReplace**. Den här koden anropar sedan **execute** på **CloudTable**-objektet med tabellen och insert- eller replace-åtgärder för tabell som parametrar. Om du vill uppdatera endast en del av en entitet går det att använda metoden **TableOperation.insertOrMerge** i stället. Observera att insert-or-replace inte stöds i den lokala lagringsemulatorn. Det betyder att den här koden endast körs vid användning av ett konto i Table Service. Du kan lära dig mer om insert-or-replace och insert-or-merge i den här [Azure Tables: Introducing Upsert and Query Projection][Azure Tables: Introducing Upsert and Query Projection] (Azure Tables: introduktion av Upsert och Query Projection).

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>Ta bort en entitet
Du kan enkelt ta bort en entitet när du har hämtat den. När entiteten har hämtats anropar du **TableOperation.delete** med den entitet som ska tas bort. Anropa sedan **execute** på **CloudTable**-objektet. Följande kod hämtar och tar bort en kundentitet.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>Ta bort en tabell
Slutligen tar följande kod bort en tabell från ett lagringskonto. I cirka 40 sekunder efter att du har tagit bort en tabell kan inte du återskapa den. 

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```
[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Azure Table Service i Java](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Azure Storage SDK för Java][Azure Storage SDK for Java]
* [Azure Storage Client SDK-referens][Azure Storage Client SDK-referens]
* [Azure Storage REST API][Azure Storage REST API]
* [Azure Storage-teamets blogg][Azure Storage Team Blog]

Mer information finns i [Azure för Java-utvecklare](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK-referens]: http://azure.github.io/azure-storage-java/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Tables: Introducing Upsert and Query Projection]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
