---
title: Hur du använder Azure Table storage eller Azure Cosmos DB tabell API från Java | Microsoft Docs
description: Lagra strukturerade data i molnet med hjälp av Azure Table Storage, en NoSQL-databas.
services: cosmos-db
documentationcenter: java
author: SnehaGunda
manager: kfile
ms.assetid: 45145189-e67f-4ca6-b15d-43af7bfd3f97
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 4ac25fd9e1d7233546b34da89eb1bcaf37f6f38b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Hur du använder Azure Table storage eller Azure Cosmos DB tabell API från Java
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Översikt
Den här artikeln visar hur du utför vanliga scenarier med hjälp av Azure Table storage-tjänst och Azure Cosmos DB. Exemplen är skrivna i Java och Använd den [Azure Storage SDK för Java][Azure Storage SDK for Java]. Scenarier som tas upp inkluderar **skapar**, **lista**, och **ta bort** tabeller, samt **infoga**, **frågar**, **ändra**, och **bort** entiteter i en tabell. Mer information om tabeller finns i [nästa steg](#next-steps) avsnitt.

> [!NOTE]
> En SDK är tillgänglig för utvecklare som använder Azure Storage på Android-enheter. Mer information finns i [Azure Storage SDK för Android][Azure Storage SDK for Android].
>

## <a name="create-an-azure-service-account"></a>Skapa ett konto i Azure-tjänst
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett Azure Cosmos DB tabell API-konto
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Skapa ett Java-program
I den här guiden använder lagringsfunktioner som du kan köra i ett Java-program lokalt eller i kod som körs i en webbroll eller worker-rollen i Azure.

Installera Java Development Kit (JDK) för att använda exemplen i den här artikeln, och sedan skapa ett Azure storage-konto i din Azure-prenumeration. När du har gjort det, verifiera att utvecklingssystemet uppfyller minsta krav och beroenden som anges i den [Azure Storage SDK för Java] [ Azure Storage SDK for Java] databasen på GitHub. Om datorn uppfyller dessa krav, kan du följa instruktionerna för att ladda ned och installera Azure Storage-biblioteken för Java i systemet från databasen. När du har slutfört uppgifterna, kan du skapa ett Java-program som använder exemplen i den här artikeln.

## <a name="configure-your-application-to-access-table-storage"></a>Konfigurera programmet att komma åt table storage
Lägg till följande importuttryck överst i Java-filen där du vill använda API: er för Azure storage eller Azure Cosmos DB tabell API åtkomst tabeller:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-an-azure-storage-connection-string"></a>Lägga till ett Azure storage-anslutningssträng
Ett Azure storage-klienten använder en anslutningssträng för lagring för att lagra slutpunkter och autentiseringsuppgifter för åtkomst till data management services. När den körs i ett klientprogram, du måste ange anslutningssträngen för lagring i följande format, med hjälp av namnet på ditt lagringskonto och den primära åtkomstnyckeln för lagringskontot som anges i den [Azure-portalen](https://portal.azure.com) för den *AccountName* och *AccountKey* värden. 

Det här exemplet visar hur du kan deklarera statiska fält att lagra anslutningssträngen:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

## <a name="add-an-azure-cosmos-db-connection-string"></a>Lägg till en Azure Cosmos DB-anslutningssträng
Ett konto i Azure Cosmos DB använder en anslutningssträng för att lagra tabell slutpunkten och dina autentiseringsuppgifter. När den körs i ett klientprogram, du måste ange anslutningssträngen för Azure Cosmos DB i följande format, med namnet på ditt konto i Azure Cosmos DB och den primära åtkomstnyckeln för kontot som anges i den [Azure-portalen](https://portal.azure.com) för den *AccountName* och *AccountKey* värden. 

Det här exemplet visar hur du kan deklarera statiska fält för Azure Cosmos DB-anslutningssträngen:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

Du kan lagra strängen i konfigurationsfilen för tjänsten i ett program som körs inom en roll i Azure, *ServiceConfiguration.cscfg*, och du kan komma åt den med ett anrop till den  **RoleEnvironment.getConfigurationSettings** metod. Här är ett exempel för att få anslutningssträng från en **inställningen** element med namnet *StorageConnectionString* i tjänstkonfigurationsfilen:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Du kan också lagra anslutningssträngen i config.properties projektfilen:

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

Följande exempel förutsätter att du har använt någon av dessa metoder för att hämta anslutningssträngen för lagring.

## <a name="create-a-table"></a>Skapa en tabell
En **CloudTableClient** objekt kan du få referensobjekt för tabeller och enheter. Följande kod skapar en **CloudTableClient** objekt och används för att skapa en ny **CloudTable** -objektet som representerar en tabell med namnet ”personer”. 

> [!NOTE]
> Det finns andra sätt att skapa **CloudStorageAccount** objekt; mer information finns i **CloudStorageAccount** i den [referens för Azure Storage Client SDK]).
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

## <a name="list-the-tables"></a>Lista tabeller
Om du vill hämta en lista över tabeller anropa den **CloudTableClient.listTables()** metod för att hämta en iterable lista över tabellnamn.

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
Entiteter mappar till Java-objekt med hjälp av en anpassad klass som implementerar **TableEntity**. För enkelhetens skull den **TableServiceEntity** klassen implementerar **TableEntity** och använder reflektion för att mappa egenskaper till get och set-metoder med namnet för egenskaper. Om du vill lägga till en entitet i en tabell, först skapa en klass som definierar egenskaperna för entiteten. Följande kod definierar en entitetsklass som använder kundens förnamn som radnyckel och efternamn som partitionsnyckel. Tillsammans identifierar en entitets partition och radnyckel entiteten i tabellen unikt. Entiteter med samma partitionsnyckel kan frågas snabbare än de som har olika partitionsnycklar.

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

Tabellåtgärder som rör entiteter kräver en **TableOperation** objekt. Det här objektet definierar åtgärden som ska utföras på en enhet som kan utföras med en **CloudTable** objekt. Följande kod skapar en ny instans av den **CustomerEntity** klass med vissa kundinformation lagras. Kod nästa anrop **TableOperation.insertOrReplace** att skapa en **TableOperation** objekt för att infoga en entitet i en tabell och associerar den nya **CustomerEntity** med den. Slutligen koden anropar den **köra** -metoden i den **CloudTable** objekt som anger tabellen ”personer” och den nya **TableOperation**, som sedan skickar en begäran till lagringstjänsten Infoga ny kundentiteten i tabellen ”personer” eller ersätta entiteten om den redan finns.

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
Du kan infoga en batch med entiteter till tabelltjänsten i samma skrivåtgärd. Följande kod skapar en **TableBatchOperation** objekt och sedan lägger till tre infogningsåtgärder till den. Varje infogningen har lagts till genom att skapa ett nytt enhetsobjekt, dess inställningsvärden och sedan anropa den **infoga** -metoden i den **TableBatchOperation** objekt att associera entiteten med en ny insert-åtgärden. Koden anropar sedan **köra** på den **CloudTable** objekt som anger tabellen ”personer” och **TableBatchOperation** -objekt som skickar batch tabellåtgärder till lagringstjänsten i en enskild begäran.

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

Några saker att tänka på batchåtgärder:

* Du kan utföra upp till 100 insert, ta bort, slå samman, ersätta, infoga eller slå samman, och infoga eller ersätta åtgärder i vilken kombination som helst i en enskild batch.
* En batchåtgärd kan ha en hämta-åtgärd om det är den enda åtgärden i batchen.
* Alla entiteter i samma batchåtgärd måste ha samma partitionsnyckel.
* En batchåtgärd är begränsad till någon 4MB nyttolast.

## <a name="retrieve-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition
Om du vill fråga en tabell för entiteter i en partition kan du använda en **TableQuery**. Anropa **TableQuery.from** att skapa en fråga på en viss tabell som returnerar en angiven resultattyp. Följande kod anger ett filter för entiteter där Partitionsnyckeln är ”Smith”. **TableQuery.generateFilterCondition** är en hjälpmetod för att skapa filter för frågor. Anropa **där** på referensen som returneras av den **TableQuery.from** metod för att tillämpa filtret i frågan. När frågan körs med ett anrop till **köra** på den **CloudTable** objekt returneras en **Iterator** med den **CustomerEntity** leda typ har angetts. Du kan sedan använda den **Iterator** returneras i en för varje loop förbruka resultaten. Den här koden skriver ut fälten för varje entitet i frågeresultatet till konsolen.

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
Om du inte vill fråga efter alla entiteter i en partition kan ange du ett intervall med jämförelseoperatorer i ett filter. Följande kod kombinerar två filter för att hämta alla entiteter i partitionen ”Smith” där Radnyckeln (Förnamn) börjar med en bokstav upp till ”E” i alfabetet. Sedan frågeresultatet skrivs ut. Om du använder enheter som lagts till i tabellen i batchen infoga i den här guiden kan bara två entiteter returneras nu (Ben och Denise Smith). Jeff Smith ingår inte.

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
Du kan skriva en fråga för att hämta en enda, specifik entitet. Följande koden anropar **TableOperation.retrieve** med partition nyckel och raden nyckel parametrar för att ange kunden ”Jeff Smith”, istället för att skapa en **TableQuery** och använda filter för att göra samma sak. När den exekveras, returnerar hämtningen endast en entitet i stället för en samling. Den **getResultAsType** metoden kastar resultatet för typ av tilldelning mål, en **CustomerEntity** objekt. Ett undantag genereras om den här typen inte är kompatibel med typen som angetts för frågan. Ett null-värde returneras om ingen enhet har en exakt partition och radnyckel matchar. Det snabbaste sättet att hämta en enskild entitet från tabelltjänsten är att ange både partitions- och radnycklar i en fråga.

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
Om du vill ändra en entitet hämtar du den från tabelltjänsten, ändrar entitetsobjektet och spara ändringarna till tabelltjänsten med en ersättning eller merge-åtgärd. Följande kod ändrar en befintlig kunds telefonnummer. Istället för att ringa **TableOperation.insert** som vi gjorde om du vill infoga den här koden anropar **TableOperation.replace**. Den **CloudTable.execute** metodanrop tabelltjänsten och entiteten ersätts om ett annat program ändrade den under tiden eftersom hämta av det här programmet. När det sker ett undantag och entiteten måste hämtas, ändras och spara igen. Optimistisk samtidighet gör mönstret är vanligt i ett distribuerat storage-system.

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
En fråga till en tabell kan hämta bara några få egenskaper från en entitet. Den här tekniken, kallad projektion, minskar bandbredden och kan förbättra frågeprestanda, i synnerhet för stora entiteter. Frågan i följande kod använder den **Välj** metod för att returnera e-postadresserna för entiteter i tabellen. Resultatet är planerad för en samling **sträng** med hjälp av en **EntityResolver**, som har typkonvertering på de enheter som returnerades från servern. Du kan lära dig mer om projektion i [Azure-tabeller: Introducing Upsert and Query Projection][Azure Tables: Introducing Upsert and Query Projection]. Observera att projektion inte stöds på den lokala lagringsemulatorn så att den här koden endast körs när med ett konto i tabelltjänsten.

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
Ofta vill du lägga till en entitet i en tabell utan att känna till om det finns redan i tabellen. En insert-eller ersätta åtgärd kan du göra en enskild begäran som infogar entiteten om den inte finns eller ersätta den befintliga versionen om den finns. Följande kod är bygger på föregående exempel, infogar eller ersätter entiteten för ”Viktor Harp”. När du har skapat en ny entitet som den här koden anropar den **TableOperation.insertOrReplace** metod. Den här koden anropar sedan **köra** på den **CloudTable** objekt med tabellen och infoga eller ersätta Tabellåtgärd som parametrar. Uppdatera endast en del av en entitet i **TableOperation.insertOrMerge** metoden kan användas i stället. Observera att infoga eller ersätta inte stöds på den lokala lagringsemulatorn så att den här koden endast körs när med ett konto i tabelltjänsten. Du kan lära dig mer om Infoga eller ersätta och infoga eller dokument i den här [Azure-tabeller: Introducing Upsert and Query Projection][Azure Tables: Introducing Upsert and Query Projection].

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
Du kan enkelt ta bort en enhet när du har hämtat den. När entiteten hämtas anropa **TableOperation.delete** med att ta bort entiteten. Sedan anropar **köra** på den **CloudTable** objekt. Följande kod hämtar och tar bort en kundentitet.

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
Följande kod tar slutligen bort en tabell från ett lagringskonto. I 40 sekunder när du tar bort en tabell, kan inte du återskapa den. 

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

* [Komma igång med Azure-tabellen Service i Java](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Azure Storage SDK för Java][Azure Storage SDK for Java]
* [Referens för Azure Storage Client SDK][referens för Azure Storage Client SDK]
* [Azure Storage REST API][Azure Storage REST API]
* [Azure Storage-teamets blogg][Azure Storage Team Blog]

Mer information finns i [Azure för Java-utvecklare](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[referens för Azure Storage Client SDK]: http://azure.github.io/azure-storage-java/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Tables: Introducing Upsert and Query Projection]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
