---
title: Hur du använder tabelltjänsten för Azure Storage eller Azure Cosmos DB tabell API från PHP | Microsoft Docs
description: Lär dig hur du använder tabellen service API från PHP för att skapa och ta bort en tabell, infoga, ta bort och fråga tabellen.
services: cosmos-db
documentationcenter: php
author: SnehaGunda
manager: kfile
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: af193c5ec7993d44fe67216843eb18f459718cfe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-storage-table-service-or-cosmos-db-table-api-from-php"></a>Hur du använder Azure-Lagringstabellen service eller Cosmos DB tabell API från PHP
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av Azure-Lagringstabellen tjänsten och Azure Cosmos DB tabell API. Exemplen är skrivna i PHP och Använd den [klientbiblioteket för Azure Storage tabell PHP][download]. Scenarier som tas upp inkluderar **skapa och ta bort en tabell**, och **lägga till, ta bort och frågar entiteter i en tabell**. Mer information om Azure Table-tjänsten finns i [nästa steg](#next-steps) avsnitt.


## <a name="create-an-azure-service-account"></a>Skapa ett konto i Azure-tjänst

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett Azure Cosmos DB tabell API-konto

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>Skapa en PHP-program

Det enda kravet att skapa en PHP-program få åtkomst till lagring tabelltjänsten eller Azure Cosmos DB tabell API är att referera till klasser i azure-lagringstabellen SDK för PHP från inom din kod. Du kan använda alla utvecklingsverktyg för att skapa programmet, inklusive anteckningar.

I den här guiden använder du Lagringstabellen tjänsten eller Azure DB som Cosmos-funktioner som kan anropas från ett PHP-program lokalt eller i koden körs i en Azure-webbroll, en arbetsroll eller en webbplats.

## <a name="get-the-client-library"></a>Hämta klientbiblioteket

1. Skapa en fil med namnet composer.json i roten av projektet och Lägg till följande kod:
```json
{
  "require": {
    "microsoft/azure-storage-table": "*"
  }
}
```
2. Hämta [composer.phar](http://getcomposer.org/composer.phar) i din rot. 
3. Öppna en kommandotolk och kör följande kommando i projektroten:
```
php composer.phar install
```
Du kan också gå till den [klientbiblioteket för Azure Storage tabell PHP](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) på GitHub att klona källkoden.


## <a name="add-required-references"></a>Lägg till nödvändiga referenser
Om du vill använda tabelltjänsten för lagring eller Azure Cosmos DB API: er, måste du:

* Referera till den automatiska bandladdaren filen med hjälp av den [require_once] [ require_once] -instruktionen och
* Referera till alla klasser som du använder.

I följande exempel visas hur du lägger till den automatiska bandladdaren fil- och referens av **TableRestProxy** klass.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

I exemplen nedan, den `require_once` instruktionen visas alltid, men endast klasserna som krävs för att köra refereras.

## <a name="add-a-storage-table-service-connection"></a>Lägga till en tabell för lagring service-anslutning
Om du vill initiera en lagring tabelltjänstens klient, måste du ha en giltig anslutningssträng. Formatet för anslutningssträngen Lagringstabellen service är:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

## <a name="add-an-azure-cosmos-db-connection"></a>Lägg till en Azure Cosmos DB-anslutning
Om du vill skapa en instans av en Azure Cosmos DB Table-klient, måste du ha en giltig anslutningssträng. Formatet för anslutningssträngen för Azure Cosmos DB är:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

## <a name="add-a-storage-emulator-connection"></a>Lägga till en anslutning för Storage-emulatorn
Åtkomst till emulatorn lagring:

```php
UseDevelopmentStorage = true
```

Om du vill skapa en Azure Table-klienten eller Azure Cosmos DB-klient, måste du använda den **TableRestProxy** klass. Du kan:

* Skicka anslutningssträngen till den eller
* Använd den **CloudConfigurationManager (CCM)** till flera externa källor för anslutningssträngen:
  * Som standard levereras den med stöd för en extern källa - miljövariabler.
  * Du kan lägga till nya källor genom att utöka den `ConnectionStringSource` klass.

Exempel som beskrivs här skickas anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Skapa en tabell
En **TableRestProxy** objekt kan du skapa en tabell med de **createTable** metod. När du skapar en tabell kan ange du tabellen service-timeout. (Mer information om service-timeout tabellen finns [inställningen tidsgränser för tabellen tjänståtgärder][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Mer information om begränsningar för tabellnamn finns [förstå den tabelltjänst-datamodellen][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Om du vill lägga till en entitet i en tabell, skapa en ny **entiteten** objekt och skicka det till **TableRestProxy -> insertEntity**. Observera att när du skapar en entitet, måste du ange en `PartitionKey` och `RowKey`. Dessa är de unika identifierarna för en entitet och är värden som kan efterfrågas mycket snabbare än andra entitetsegenskaper. Systemet använder `PartitionKey` att automatiskt distribuera tabellens entiteter över många lagringsnoder. Entiteter med samma `PartitionKey` lagras på samma nod. (Utföra åtgärder på flera entiteter som lagras på samma nod bättre än på entiteter som lagras på olika noder.) Den `RowKey` är unikt ID för en entitet i en partition.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Information om Tabellegenskaper och typer finns [förstå den tabelltjänst-datamodellen][table-data-model].

Den **TableRestProxy** klass erbjuder två alternativa metoder för att lägga till entiteter: **insertOrMergeEntity** och **insertOrReplaceEntity**. Om du vill använda metoderna, skapa en ny **entiteten** och skicka den som en parameter till någon av metoderna. Varje metod infogar entiteten om den inte finns. Om enheten redan **insertOrMergeEntity** uppdaterar egenskapsvärden om egenskaperna redan finns och lägger till nya egenskaper om det inte finns, medan **insertOrReplaceEntity** ersätter en befintlig entitet. I följande exempel visas hur du använder **insertOrMergeEntity**. Om entiteten med `PartitionKey` ”tasksSeattle” och `RowKey` ”1” inte redan finns, kommer att infogas. Men om det har tidigare infogats (som visas i exemplet ovan), den `DueDate` egenskapen uppdateras och `Status` egenskapen har lagts till. Den `Description` och `Location` egenskaper har också uppdaterats, men med värden som effektivt låt dem vara oförändrad. Om dessa senare två egenskaper inte har lagts till som visas i exemplet, men fanns på målentiteten, skulle befintliga värden ändras inte.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Hämta en enda entitet
Den **TableRestProxy -> getEntity** metod kan du hämta en enda enhet genom att fråga om dess `PartitionKey` och `RowKey`. I exemplet nedan Partitionsnyckeln `tasksSeattle` och radnyckel `1` skickas till den **getEntity** metod.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Hämta alla entiteter i en partition
Entiteten frågor har skapats med hjälp av filter (Mer information finns i [frågor till tabeller och de entiteter][filters]). Om du vill hämta alla entiteter i partitionen, använder du filtret ”PartitionKey eq *partitionsnamn*”. I följande exempel visas hur du hämtar alla entiteter i den `tasksSeattle` partition genom att skicka ett filter för att den **queryEntities** metod.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Hämta en delmängd av entiteter i en partition
Samma mönster som används i föregående exempel kan användas för att hämta alla entiteter i en partition. Delmängden av du hämta entiteter bestäms av filtret som du använder (Mer information finns i [frågor till tabeller och de entiteter][filters]). I följande exempel visas hur du använder ett filter för att hämta alla entiteter med ett visst `Location` och en `DueDate` mindre än det angivna datumet.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Hämta en deluppsättning Entitetsegenskaper
En fråga kan hämta en deluppsättning entitetsegenskaper. Den här tekniken, kallad *projektion*, minskar bandbredden och kan förbättra frågeprestanda, särskilt för stora entiteter. Om du vill ange en egenskap för att hämta namnet på egenskapen som skickar den **addSelectField ->** metod. Du kan anropa metoden flera gånger för att lägga till fler egenskaper. Efter körning **TableRestProxy -> queryEntities**, returnerade entiteter har endast de markerade egenskaperna. (Om du vill returnera en delmängd av tabellentiteter använda ett filter som visas i frågor ovan.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Uppdatera en entitet
Du kan uppdatera en befintlig entitet med hjälp av den **entitet -> setProperty** och **entitet -> addProperty** metoder på enheten och sedan anropar **TableRestProxy -> updateEntity**. I följande exempel hämtar en entitet, ändrar en egenskap, tar bort en annan egenskap och lägger till en ny egenskap. Du kan ta bort en egenskap genom att ange värdet till **null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Ta bort en entitet
Om du vill ta bort en entitet, klara tabellnamnet och entitetens `PartitionKey` och `RowKey` till den **TableRestProxy -> deleteEntity** metod.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

För kontroller av samtidighet, kan du ange Etag för en entitet som ska tas bort med hjälp av den **DeleteEntityOptions -> setEtag** metod och skicka den **DeleteEntityOptions** objekt-  **deleteEntity** som en fjärde parameter.

## <a name="batch-table-operations"></a>Batchtabeller
Den **TableRestProxy -> batch** metoden kan du utföra flera åtgärder i en enskild begäran. Det här mönstret måste du lägga till åtgärder **BatchRequest** objektet och sedan överföra den **BatchRequest** objekt den **TableRestProxy -> batch** metoden. Att lägga till en åtgärd för att en **BatchRequest** objekt, kan du anropa någon av följande metoder flera gånger:

* **addInsertEntity** (lägger till en insertEntity åtgärd)
* **addUpdateEntity** (lägger till en updateEntity åtgärd)
* **addMergeEntity** (lägger till en åtgärd för mergeEntity)
* **addInsertOrReplaceEntity** (lägger till en insertOrReplaceEntity åtgärd)
* **addInsertOrMergeEntity** (lägger till en insertOrMergeEntity åtgärd)
* **addDeleteEntity** (lägger till en åtgärd för deleteEntity)

I följande exempel visas hur du kör **insertEntity** och **deleteEntity** åtgärder i en enskild begäran. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Mer information om batchbearbetning tabellåtgärder finns [utför entitet gruppera transaktioner][entity-group-transactions].

## <a name="delete-a-table"></a>Ta bort en tabell
Slutligen skicka tabellnamnet om du vill ta bort en tabell i **TableRestProxy -> deleteTable** metod.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Table-tjänsten och Azure Cosmos DB följa dessa länkar om du vill veta mer.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.

* [PHP Developer Center](/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: http://php.net/require_once
[table-service-timeouts]: https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: https://docs.microsoft.com/rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: https://docs.microsoft.com/rest/api/storageservices/Performing-Entity-Group-Transactions
