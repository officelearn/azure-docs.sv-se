---
title: Migrera från CouchBase till Azure Cosmos DB SQL API
description: Steg-för-steg-vägledning för migrering från CouchBase till Azure Cosmos DB SQL API
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210949"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrera från CouchBase till Azure Cosmos DB SQL API

Azure Cosmos DB är en skalbar, globalt distribuerad, fullständigt hanterad databas. Det ger garanterad låg latens åtkomst till dina data. Mer information om Azure Cosmos DB finns i [översiktsartikeln.](introduction.md) Den här artikeln innehåller instruktioner för att migrera Java-program som är anslutna till Couchbase till ett SQL API-konto i Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Skillnader i nomenklatur

Följande är de viktigaste funktionerna som fungerar annorlunda i Azure Cosmos DB jämfört med Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Couchbase server| Konto       |
|Hink           | Databas      |
|Hink           | Behållare/samling |
|JSON-dokument    | Objekt/dokument |

## <a name="key-differences"></a>Viktiga skillnader

* Azure Cosmos DB har ett "ID" fält i dokumentet medan Couchbase har ID som en del av bucket. "ID"-fältet är unikt över hela partitionen.

* Azure Cosmos DB skalar med hjälp av partitionerings- eller sharding-tekniken. Vilket innebär att den delar upp data i flera shards/partitioner. Dessa partitioner/shards skapas baserat på egenskapen partitionsnyckel som du anger. Du kan välja partitionsnyckel för att optimera läs-och skrivåtgärder eller läs/skriv optimerad också. Mer information finns i [partitioneringsartikeln.](./partition-data.md)

* I Azure Cosmos DB krävs det inte för hierarkin på den översta nivån att beteckna samlingen eftersom samlingsnamnet redan finns. Den här funktionen gör JSON-strukturen mycket enklare. Följande är ett exempel som visar skillnader i datamodellen mellan Couchbase och Azure Cosmos DB:

   **Couchbase**: Dokument-ID = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: Se "ID" i dokumentet som visas nedan

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Support för Java SDK

Azure Cosmos DB har följande SDK:er för att stödja olika Java-ramverk:

* Async SDK
* Fjäder boot SDK

I följande avsnitt beskrivs när var och en av dessa SDK:er ska användas. Tänk dig ett exempel där vi har tre typer av arbetsbelastningar:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase som dokumentdatabas & vårens databaserade anpassade frågor

Om arbetsbelastningen som du migrerar baseras på Spring Boot Based SDK kan du använda följande steg:

1. Lägg till överordnad i FILEN POM.xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Lägg till egenskaper i FILEN POM.xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Lägg till beroenden i FILEN POM.xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Lägg till programegenskaper under resurser och ange följande. Se till att ersätta parametrarna för URL,nyckel och databasnamn:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Definiera namnet på samlingen i modellen. Du kan också ange ytterligare anteckningar. Till exempel ID, partitionsnyckel för att beteckna dem uttryckligen:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Följande är kodavsnitten för CRUD-åtgärder:

### <a name="insert-and-update-operations"></a>Infoga och uppdatera åtgärder

Där *_repo* är föremål för databasen och *doc* är POJO-klassens objekt. Du kan `.save` använda för att infoga eller upsert (om dokument med angivet ID hittades). Följande kodavsnitt visar hur du infogar eller uppdaterar ett doc-objekt:

```_repo.save(doc);```

### <a name="delete-operation"></a>Ta bort åtgärd

Tänk på följande kodavsnitt, där doc-objektet kommer att ha ID och partitionsnyckel obligatoriskt för att hitta och ta bort objektet:

```_repo.delete(doc);```

### <a name="read-operation"></a>Läs operation

Du kan läsa dokumentet med eller utan att ange partitionsnyckeln. Om du inte anger partitionsnyckeln behandlas den som en fråga mellan partitioner. Tänk på följande kodexempel, först en kommer att utföra operation med ID och partition nyckelfält. I det andra exemplet används ett vanligt fält & utan att ange partitionsnyckelfältet.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

Det är allt, du kan nu använda ditt program med Azure Cosmos DB. Fullständigt kodexempel för det exempel som beskrivs i det här dokumentet är tillgängligt i [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub-repo.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase som en dokumentdatabas & med N1QL-frågor

N1QL-frågor är sättet att definiera frågor i Couchbase.

|N1QL-fråga | Azure CosmosDB-fråga|
|-------------------|-------------------|
|VÄLJ`TravelDocument`META( ).id AS `TravelDocument`id, `_type` .* VARIFRÅN `TravelDocument` = "com.xx.xx.xx.xxx.xxx.xxxx " och land = 'Indien' och ANY m i Visum TILLSFIES m.type == ` Validity` 'Multi-Entry' och m.Country IN ['Indien', Bhutan'] ORDER BY DESC LIMIT 25 OFFSET 0   | VÄLJ c.id,c FRÅN c JOIN m i c.country='Indien' WHERE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" och c.country = "Indien" och m.type = "Multi-Entry" och m.Country IN ("Indien", "Bhutan") ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

Du kan märka följande ändringar i dina N1QL-frågor:

* Du behöver inte använda nyckelordet META eller referera till dokumentet på första nivån. Istället kan du skapa en egen referens till behållaren. I det här exemplet har vi betraktat det som "c" (det kan vara vad som helst). Den här referensen används som prefix för alla fält på första nivån. Fr exempel, c.id, c.country etc.

* Istället för "ANY" nu kan du göra en koppling på underdokument och hänvisa den med ett dedikerat alias som "m". När du har skapat alias för ett underdokument måste du använda alias. Till exempel m.Country.

* Sekvensen av FÖRSKJUTNING är annorlunda i Azure Cosmos DB fråga, först måste du ange FÖRSKJUTNING sedan BEGRÄNSA. Vi rekommenderar att du inte använder Spring Data SDK om du använder maximala anpassade definierade frågor eftersom det kan ha onödiga omkostnader på klientsidan medan du skickar frågan till Azure Cosmos DB. Istället har vi en direkt Async Java SDK, som kan utnyttjas mycket effektivt i detta fall.

### <a name="read-operation"></a>Läsåtgärd

Använd Async Java SDK med följande steg:

1. Konfigurera följande beroende på FILEN POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Skapa ett anslutningsobjekt för Azure `ConnectionBuilder` Cosmos DB med den metod som visas i följande exempel. Se till att du sätter denna deklaration i bönan så att följande kod ska köras endast en gång:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Om du vill köra frågan måste du köra följande kodavsnitt:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Nu, med hjälp av ovanstående metod kan du skicka flera frågor och köra utan krångel. Om du har kravet på att köra en stor fråga, som kan delas upp i flera frågor, försök sedan med följande kodavsnitt i stället för det föregående:

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

Med den tidigare koden kan du köra frågor parallellt och öka de distribuerade körningarna för att optimera. Ytterligare kan du köra in- och uppdateringsåtgärderna också:

### <a name="insert-operation"></a>Infoga åtgärd

Om du vill infoga dokumentet kör du följande kod:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Prenumerera sedan på Mono som:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Upsert-drift

Upsert-åtgärden kräver att du anger det dokument som behöver uppdateras. Om du vill hämta hela dokumentet kan du använda det utdrag som nämns under rubriken läsåtgärd och sedan ändra de fält som krävs. Följande kodavsnitt upserts dokumentet:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Prenumerera sedan på mono. Se kodavsnittet för monoprenumeration i infogning.

### <a name="delete-operation"></a>Ta bort åtgärd

Följande utdrag kommer att ta bort åtgärden:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Prenumerera sedan på mono, se mono prenumerationskodavsnitt i infogningsåtgärden. Det fullständiga kodexemplet finns i [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub-repo.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase som nyckel/värdepar

Det här är en enkel typ av arbetsbelastning där du kan utföra uppslag i stället för frågor. Gör så här för nyckel-/värdepar:

1. Överväg att ha "/ID" som primärnyckel, vilket gör att du kan utföra uppslag direkt i den specifika partitionen. Skapa en samling och ange "/ID" som partitionsnyckel.

1. Stäng av indexeringen helt. Eftersom du ska utföra uppslagsåtgärder finns det ingen anledning att redovisa indexeringskostnader. Om du vill inaktivera indexering loggar du in på Azure Portal, goto Azure Cosmos DB-konto. Öppna **Data Explorer,** välj **databasen** och **behållaren**. Öppna fliken **Skala & inställningar** och välj **indexeringsprincipen**. Indexeringsprincipen ser ut så här:
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   Ersätt ovanstående indexeringsprincip med följande princip:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. Använd följande kodavsnitt för att skapa anslutningsobjektet. Anslutningsobjekt (som @Bean ska placeras i eller göra det statiskt):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Nu kan du utföra CRUD-åtgärderna enligt följande:

### <a name="read-operation"></a>Läsåtgärd

Om du vill läsa objektet använder du följande kodavsnitt:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Infoga åtgärd

Om du vill infoga ett objekt kan du utföra följande kod:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Prenumerera sedan på mono som:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert-drift

Om du vill uppdatera värdet för en artikel läser du kodavsnittet nedan:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Prenumerera sedan på mono, se mono prenumerationskodavsnitt i infogningsåtgärden.

### <a name="delete-operation"></a>Ta bort åtgärd

Använd följande kodavsnitt för att köra borttagningsåtgärden:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Prenumerera sedan på mono, se mono prenumerationskodavsnitt i infogningsåtgärden. Det fullständiga kodexemplet finns i [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub-repo.

## <a name="data-migration"></a>Datamigrering

Det finns två sätt att migrera data.

* **Använd Azure Data Factory:** Detta är den mest rekommenderade metoden för att migrera data. Konfigurera källan som Couchbase och sink som Azure Cosmos DB SQL API, se azure [Cosmos DB Data Factory-anslutningsartikeln](../data-factory/connector-azure-cosmos-db.md) för detaljerade steg.

* **Använd verktyget för import av Azure Cosmos DB-data:** Det här alternativet rekommenderas att migrera med virtuella datorer med mindre mängd data. Detaljerade steg finns i artikeln [Dataimportör.](./import-data.md)

## <a name="next-steps"></a>Efterföljande moment

* Om du vill göra prestandatester läser du [Prestanda- och skalningstestning med Azure Cosmos](./performance-testing.md) DB-artikel.
* Information om hur du optimerar koden finns i [Prestandatips för Azure Cosmos](./performance-tips-async-java.md) DB-artikel.
* Utforska Java Async V3 SDK, [SDK referens](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub repo.
