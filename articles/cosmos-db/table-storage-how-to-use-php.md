---
title: "Använda table storage från PHP | Microsoft Docs"
description: "Lär dig hur du använder tjänsten tabell från PHP skapa och ta bort en tabell, infoga, ta bort och fråga tabellen."
services: cosmos-db
documentationcenter: php
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 5cfdfc814d1dfc15149d7b100ba1f6b68e90bb36
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-use-azure-table-storage-from-php"></a>Hur du använder Azure Table storage från PHP
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av tjänsten Azure Table. Exemplen är skrivna i PHP och Använd den [Azure SDK för PHP][download]. Scenarier som tas upp inkluderar **skapar och tar bort en tabell och lägga till, ta bort och frågar entiteter i en tabell**. Mer information om Azure Table-tjänsten finns i [nästa steg](#next-steps) avsnitt.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Skapa en PHP-program
Det enda kravet för att skapa en PHP-program som använder tjänsten Azure Table är den refererande klasser i Azure SDK för PHP från inom din kod. Du kan använda alla utvecklingsverktyg för att skapa programmet, inklusive anteckningar.

I den här guiden kan du använda tabellen tjänstens funktioner som kan anropas från ett PHP-program lokalt eller i koden körs i en Azure-webbroll, en arbetsroll eller en webbplats.

## <a name="get-the-azure-client-libraries"></a>Hämta Azures klientbibliotek
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Konfigurera ditt program för att få åtkomst till tabelltjänsten
Om du vill använda tjänsten Azure Table API: er, måste du:

1. Referera till den automatiska bandladdaren filen med hjälp av den [require_once] [ require_once] -instruktionen och
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du lägger till den automatiska bandladdaren fil- och referens av **ServicesBuilder** klass.

> [!NOTE]
> Exemplen i den här artikeln förutsätter att du har installerat PHP-klientbibliotek för Azure via Composer. Om du har installerat biblioteken manuellt, måste du referera till den <code>WindowsAzure.php</code> bandladdaren filen.
>
>

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

I exemplen nedan, den `require_once` instruktionen visas alltid, men endast klasserna som krävs för att köra refereras.

## <a name="set-up-an-azure-storage-connection"></a>Skapa en Azure storage-anslutning
Du måste ha en giltig anslutningssträng för att initiera en Azure Table service-klient. Formatet för anslutningssträng för tabellen är:

För att komma åt en live-tjänst:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

För att komma åt lagringsplatsen emulatorn:

```php
UseDevelopmentStorage=true
```

För att skapa någon Azure-tjänst-klient, måste du använda den **ServicesBuilder** klass. Du kan:

* Skicka anslutningssträngen till den eller
* Använd den **CloudConfigurationManager (CCM)** till flera externa källor för anslutningssträngen:
  * som standard levereras med stöd för en extern källa - miljövariabler
  * Du kan lägga till nya källor genom att utöka den **ConnectionStringSource** klass

Exempel som beskrivs här skickas anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
```

## <a name="create-a-table"></a>Skapa en tabell
En **TableRestProxy** objekt kan du skapa en tabell med de **createTable** metod. När du skapar en tabell kan ange du tabellen service-timeout. (Mer information om service-timeout tabellen finns [inställningen tidsgränser för tabellen tjänståtgärder][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Create table.
    $tableRestProxy->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
}
```

Mer information om begränsningar för tabellnamn finns [förstå den tabelltjänst-datamodellen][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell
Om du vill lägga till en entitet i en tabell, skapa en ny **entiteten** objekt och skicka det till **TableRestProxy -> insertEntity**. Observera att när du skapar en entitet, måste du ange en `PartitionKey` och `RowKey`. Dessa är de unika identifierarna för en entitet och är värden som kan efterfrågas mycket snabbare än andra entitetsegenskaper. Systemet använder `PartitionKey` att automatiskt distribuera tabellens entiteter över många lagringsnoder. Entiteter med samma `PartitionKey` lagras på samma nod. (Utföra åtgärder på flera entiteter som lagras på samma nod bättre än på entiteter som lagras på olika noder.) Den `RowKey` är unikt ID för en entitet i en partition.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableRestProxy->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Information om Tabellegenskaper och typer finns [förstå den tabelltjänst-datamodellen][table-data-model].

Den **TableRestProxy** klass erbjuder två alternativa metoder för att lägga till entiteter: **insertOrMergeEntity** och **insertOrReplaceEntity**. Om du vill använda metoderna, skapa en ny **entiteten** och skicka den som en parameter till någon av metoderna. Varje metod infogar entiteten om den inte finns. Om enheten redan **insertOrMergeEntity** uppdaterar egenskapsvärden om egenskaperna redan finns och lägger till nya egenskaper om det inte finns, medan **insertOrReplaceEntity** ersätter en befintlig entitet. I följande exempel visas hur du använder **insertOrMergeEntity**. Om entiteten med `PartitionKey` ”tasksSeattle” och `RowKey` ”1” inte redan finns, kommer att infogas. Men om det har tidigare infogats (som visas i exemplet ovan), den `DueDate` egenskapen uppdateras och `Status` egenskapen kommer att läggas till. Den `Description` och `Location` egenskaper har också uppdaterats, men med värden som effektivt låt dem vara oförändrad. Om dessa senare två egenskaper inte har lagts till som visas i exemplet, men fanns på målentiteten, skulle befintliga värden ändras inte.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

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
    $tableRestProxy->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Hämta en enda entitet
Den **TableRestProxy -> getEntity** metod kan du hämta en enda enhet genom att fråga om dess `PartitionKey` och `RowKey`. I exemplet nedan Partitionsnyckeln `tasksSeattle` och radnyckel `1` skickas till den **getEntity** metod.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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
En fråga kan hämta en deluppsättning entitetsegenskaper. Den här tekniken, kallad *projektion*, minskar bandbredden och kan förbättra frågeprestanda, särskilt för stora entiteter. Om du vill ange en egenskap som ska hämtas skicka namnet på egenskapen till den **addSelectField ->** metod. Du kan anropa metoden flera gånger för att lägga till fler egenskaper. Efter körning **TableRestProxy -> queryEntities**, returnerade entiteter har endast de markerade egenskaperna. (Om du vill returnera en delmängd av tabellentiteter använda ett filter som visas i frågor ovan.)

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableRestProxy->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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
En befintlig entitet kan uppdateras med hjälp av den **entitet -> setProperty** och **entitet -> addProperty** metoder på enheten och sedan anropar **TableRestProxy -> updateEntity**. I följande exempel hämtar en entitet, ändrar en egenskap, tar bort en annan egenskap och lägger till en ny egenskap. Du kan ta bort en egenskap genom att ange värdet till **null**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();

$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

$entity->setPropertyValue("Location", null); //Removed Location.

$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableRestProxy->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Ta bort en entitet
Om du vill ta bort en entitet, klara tabellnamnet och entitetens `PartitionKey` och `RowKey` till den **TableRestProxy -> deleteEntity** metod.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete entity.
    $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Observera att du kan ange Etag för en entitet som ska tas bort med hjälp av för samtidighet kontrollerar den **DeleteEntityOptions -> setEtag** metod och skicka den **DeleteEntityOptions** objekt- **deleteEntity** som en fjärde parameter.

## <a name="batch-table-operations"></a>Batchtabeller
Den **TableRestProxy -> batch** metoden kan du utföra flera åtgärder i en enskild begäran. Det här mönstret måste du lägga till åtgärder **BatchRequest** objektet och sedan överföra den **BatchRequest** objekt den **TableRestProxy -> batch** metoden. Att lägga till en åtgärd för att en **BatchRequest** objekt, kan du anropa någon av följande metoder flera gånger:

* **addInsertEntity** (lägger till en insertEntity åtgärd)
* **addUpdateEntity** (lägger till en updateEntity åtgärd)
* **addMergeEntity** (lägger till en åtgärd för mergeEntity)
* **addInsertOrReplaceEntity** (lägger till en insertOrReplaceEntity åtgärd)
* **addInsertOrMergeEntity** (lägger till en insertOrMergeEntity åtgärd)
* **addDeleteEntity** (lägger till en åtgärd för deleteEntity)

I följande exempel visas hur du kör **insertEntity** och **deleteEntity** åtgärder i en enskild begäran:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

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
    $tableRestProxy->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete table.
    $tableRestProxy->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Table-tjänsten kan du följa dessa länkar att lära dig mer komplexa lagringsuppgifter.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.

* [PHP Developer Center](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
