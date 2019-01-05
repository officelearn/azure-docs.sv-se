---
title: Arbeta med Azure Cosmos DB-databaser, behållare och objekt
description: Den här artikeln beskriver hur du skapar och använder Azure Cosmos DB-databaser, behållare och objekt
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 23400331db3bd63eeeb843c9772604fabf5a87d1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043778"
---
# <a name="working-with-azure-cosmos-databases-containers-and-items"></a>Arbeta med Azure Cosmos-databaser, behållare och objekt

När du har skapat en [Azure Cosmos DB-konto](account-overview.md) under din Azure-prenumeration kan du hantera data i ditt konto genom att skapa databaser, behållare och objekt. Den här artikeln beskrivs var och en av dessa entiteter: databaser, behållare och objekt. Följande bild visar hierarkin för olika enheter i ett Azure Cosmos-konto:

![Azure Cosmos-konto-entiteter](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos-databaser

Du kan skapa en eller flera Azure-Cosmos-databaser under ditt konto. En databas är detsamma som en namnrymd, är det hanteringsenheten för en uppsättning Azure Cosmos-behållare. I följande tabell visas hur en Azure Cosmos-databas är mappad till olika API-specifika entiteter:

| **Azure Cosmos-enhet** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-databas | Databas | Nyckelutrymme | Databas | Databas | Ej tillämpligt |

> [!NOTE]
> När du skapar din första tabellen med tabell-API: er-konton kan skapas en standarddatabas automatiskt i ditt Azure Cosmos-konto.

### <a name="operations-on-an-azure-cosmos-database"></a>Åtgärder på en Azure Cosmos-databas

Du kan interagera med en Azure Cosmos-databas med hjälp av de följande Azure-Cosmos API: er:

| **Åtgärd** | **Azure CLI**|**SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- | --- |
|Räkna upp alla databaser| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |
|Läsa databas| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |
|Skapa ny databas| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |
|Uppdatera databas| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-behållare

En Azure Cosmos-behållare är enheten skalbarhet för både etablerat dataflöde och lagring av objekt. En behållare är horisontellt partitionerade och replikeras sedan över flera regioner. De element som du lägger till behållaren och dataflödet att etablera på det både är automatiskt fördelade över en uppsättning logiska partitioner baserat på partitionsnyckel. Mer information om partitionering och partitionsnyckel finns [logiska partitioner](partition-data.md) artikeln. 

När du skapar en Azure Cosmos-behållare kan konfigurera du dataflöde på något av följande lägen:

* **Dedikerat dataflöde** läge: Dataflödet som etableras i en behållare enbart för det och det backas upp av att serviceavtalen. Mer information finns i [hur du etablera dataflöde för en Azure Cosmos-behållare](how-to-provision-container-throughput.md).

* **Delade etablerat dataflöde** läge: De här behållarna dela det etablerade dataflödet som med andra behållare i samma databas (förutom de här behållarna som har konfigurerats med dedikerad etablerat dataflöde). Med andra ord delas dataflöde i databasen mellan alla ”delade” behållare. Mer information finns i [så här konfigurerar du etablerat dataflöde på en Azure Cosmos-databas](how-to-provision-database-throughput.md).

En Azure Cosmos-behållare kan Elastiskt skala, oavsett om du skapar behållare med antingen ”delade” eller ”dedikerad” etablerat dataflöde lägen.

En Azure Cosmos-behållare är en schemaoberoende behållare för objekt. Objekt i en behållare kan ha valfri scheman. Till exempel kan ett objekt som representerar en person, ett objekt som representerar en bil placeras i samma behållare. Som standard indexera alla objekt som du lägger till en behållare automatiskt utan att kräva något explicit index eller schemahantering. Du kan anpassa beteendet indexering genom att konfigurera indexprincip på en behållare. 

Du kan ange Time To Live (TTL) för markerade objekt i en Azure Cosmos-behållare eller för hela behållaren att smidigt rensa de objekt från systemet. Azure Cosmos DB kommer automatiskt att ta bort objekten när de går ut. Det garanterar även att inte returnerar en fråga som utförs på behållaren har upphört att gälla objekt i en fast gräns. Mer information finns i [hur du konfigurerar TTL på din behållare](how-to-time-to-live.md).

Med ändringsflödet kan prenumerera du till åtgärdsloggen som hanteras för varje logisk partition behållarens. Change Feed innehåller en logg för alla uppdateringar som utförs på behållaren tillsammans med de före och efter-bilder av objekten. Se [skapa reaktiva program med hjälp av ändringen feed](change-feed.md). Du kan också konfigurera kvarhållningsvaraktighetens för ändringsflödet med ändringsflödet i principen för behållaren. 

Du kan registrera lagrade procedurer, utlösare, användardefinierade funktioner (UDF) och sammanfoga procedurer med din Azure Cosmos-behållare. 

Du kan ange en unik nyckel för Azure Cosmos-behållaren. Genom att skapa en unik nyckel princip kan kontrollera du unikhet för ett eller flera värden per logisk partitionsnyckel. När du har skapat en behållare med en unik nyckel princip förhindrar skapande av alla nya eller uppdaterade objekt med värden som duplicerar värdena som anges av den unika key-begränsningen. Mer information finns i [unika nyckelvillkor](unique-keys.md).

En Azure Cosmos-behållare är specialiserat till API-specifika entiteter på följande sätt:

| **Azure Cosmos-enhet** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-behållare | Samling | Tabell | Samling | Graph | Tabell |

### <a name="properties-of-an-azure-cosmos-container"></a>Egenskaperna för en Azure Cosmos-behållare

En Azure Cosmos-behållare har en uppsättning systemdefinierade egenskaper. Beroende på valet av API: et, kan några av dem inte exponeras direkt. I följande tabell beskrivs i listan över stöds systemdefinierade egenskaper:

| **Systemdefinierade egenskapen** | **Datorer som genererade eller användaren inställbar** | **Syfte** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | Systemgenererad | Unik identifierare för behållare | Ja | Nej | Nej | Nej | Nej |
|_etag | Systemgenererad | Enhetstagg som används för optimistisk samtidighetskontroll | Ja | Nej | Nej | Nej | Nej |
|_ts | Systemgenererad | Senast uppdaterade tidsstämpeln i behållaren | Ja | Nej | Nej | Nej | Nej |
|_self | Systemgenererad | Adresserbara URI: N för behållaren | Ja | Nej | Nej | Nej | Nej |
|id | Konfigureras av användaren | Användardefinierade unika namnet på behållaren | Ja | Ja | Ja | Ja | Ja |
|indexingPolicy | Konfigureras av användaren | Ger möjlighet att ändra sökvägen index, deras precision och konsekvensmodellen. | Ja | Nej | Nej | Nej | Ja |
|TimeToLive | Konfigureras av användaren | Ger möjlighet att ta bort objekt automatiskt från en behållare efter en viss tidsperiod. Mer information finns i den [Time To Live](time-to-live.md) artikeln. | Ja | Nej | Nej | Nej | Ja |
|changeFeedPolicy | Konfigureras av användaren | Används för att läsa ändrade objekt i en behållare. Mer information finns i den [ändringsflödet](change-feed.md) artikeln. | Ja | Nej | Nej | Nej | Ja |
|uniqueKeyPolicy | Konfigureras av användaren | Du se till att en eller flera värden i en logisk partition unikt med unika nycklar. Mer information finns i den [unika nycklar](unique-keys.md) artikeln. | Ja | Nej | Nej | Nej | Ja |

### <a name="operations-on-an-azure-cosmos-container"></a>Åtgärder på en Azure Cosmos-behållare

En Azure Cosmos-behållare har stöd för följande åtgärder med hjälp av någon av Azure Cosmos-API: er.

| **Åtgärd** | **Azure CLI** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Räkna upp behållare i en databas | Ja* | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |
| Läsa en behållare | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |
| Skapa ny behållare | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |
| Uppdatera behållare | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |
| Ta bort behållare | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |

## <a name="azure-cosmos-items"></a>Azure Cosmos-objekt

Beroende på valet av API: et, kan ett Azure Cosmos-objekt representera antingen ett dokument i en samling, en rad i en tabell eller en nod/edge i ett diagram. I följande tabell visar mappningen mellan API-specifika entiteter till ett Azure Cosmos-objekt:

| **Cosmos-entitet** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-objekt | Dokument | Rad | Dokument | Nod eller Kanttabell | Objekt |

### <a name="properties-of-an-item"></a>Egenskaper för ett objekt

Varje Azure Cosmos-objekt har följande systemdefinierade egenskaper. Beroende på valet av API: et, kan några av dem inte exponeras direkt.

|**Systemdefinierade egenskapen** | **Datorer som genererade eller användaren inställbar**| **Syfte** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_id | Systemgenererad | Unik identifierare för objektet | Ja | Nej | Nej | Nej | Nej |
|_etag | Systemgenererad | Enhetstagg som används för optimistisk samtidighetskontroll | Ja | Nej | Nej | Nej | Nej |
|_ts | Systemgenererad | Senast uppdaterade tidsstämpeln för objektet | Ja | Nej | Nej | Nej | Nej |
|_self | Systemgenererad | Adresserbara URI: N för objektet | Ja | Nej | Nej | Nej | Nej |
|id | Antingen | Användardefinierade unikt namn inom en logisk partition. Om användaren inte anger id, systemet automatiskt att generera en. | Ja | Ja | Ja | Ja | Ja |
|Godtycklig användardefinierade egenskaper | Användardefinierade | Användardefinierade egenskaper som visas i API-intern representation (JSON, BSON, CQL osv.) | Ja | Ja | Ja | Ja | Ja |

### <a name="operations-on-items"></a>Åtgärder på objekt

Azure Cosmos-objektet har stöd för följande åtgärder som kan utföras med hjälp av någon av Azure Cosmos-API: er.

| **Åtgärd** | **Azure CLI** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Infoga, Ersätt, ta bort, Upsert, Läs | Nej | Ja | Ja | Ja | Ja | Ja |

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta att lära dig att etablera dataflöde för Azure Cosmos-konto eller finns i andra begrepp:

* [Så här konfigurerar du etablerat dataflöde på en Azure Cosmos-databas](how-to-provision-database-throughput.md)
* [Så här konfigurerar du etablerat dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md)
* [Logiska partitioner](partition-data.md)
* [Så här konfigurerar du TTL för Azure Cosmos-behållare](how-to-time-to-live.md)
* [Skapa reaktiva program med hjälp av Change Feed](change-feed.md)
* [Så här konfigurerar du unika nyckelrestriktion i din Azure Cosmos-behållare](unique-keys.md)
