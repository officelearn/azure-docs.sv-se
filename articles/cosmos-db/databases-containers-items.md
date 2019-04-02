---
title: Arbeta med Azure Cosmos DB-databaser, behållare och objekt
description: Den här artikeln beskriver hur du skapar och använder Azure Cosmos DB-databaser, behållare och objekt
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: f3bec1b279c07e62e246ebfa933b3942e38406de
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762913"
---
# <a name="work-with-databases-containers-and-items"></a>Arbeta med databaser, containrar och objekt

När du har skapat en [Azure Cosmos-konto](account-overview.md) under din Azure-prenumeration kan du hantera data i ditt konto genom att skapa databaser, behållare och objekt. Den här artikeln beskrivs var och en av dessa entiteter: databaser, behållare och objekt. Följande bild visar hierarkin för olika enheter i ett Azure Cosmos-konto:

![Azure Cosmos-konto-entiteter](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos-databaser

Du kan skapa en eller flera Azure-Cosmos-databaser under ditt konto. En databas är detsamma som ett namnområde. Det är hanteringsenheten för en uppsättning Azure Cosmos-behållare. I följande tabell visas hur en Azure Cosmos-databas är mappad till olika API-specifika entiteter:

| **Azure Cosmos-enhet** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-databas | Databas | Nyckelutrymme | Databas | Databas | Ej tillämpligt |

> [!NOTE]
> När du skapar din första tabellen med tabell-API-konton kan skapas en standarddatabas automatiskt i ditt Azure Cosmos-konto.

### <a name="operations-on-an-azure-cosmos-database"></a>Åtgärder på en Azure Cosmos-databas

Du kan interagera med en Azure Cosmos-databas med Azure Cosmos-API: er på följande sätt:

| **Åtgärd** | **Azure CLI**|**SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- | --- |
|Räkna upp alla databaser| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |
|Läsa databas| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |
|Skapa ny databas| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |
|Uppdatera databas| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-behållare

En Azure Cosmos-behållare är Faktureringsenhet skalbarhet för både etablerat dataflöde och lagring. En behållare är horisontellt partitionerade och replikeras sedan över flera regioner. De objekt som du lägger till behållaren och det dataflöde som du etablerar på den distribueras automatiskt i en uppsättning logiska partitioner baserat på partitionsnyckel. Mer information om partitionering och partitionsnycklar finns [detta](partition-data.md) artikeln. 

När du skapar en Azure Cosmos-behållare kan konfigurera du dataflöde på något av följande lägen:

* **Dedikerat dataflöde** läge: Dataflödet som tillhandahållits i en behållare enbart för behållaren och den backas upp av att serviceavtalen. Mer information finns i [hur du etablera dataflöde för en Azure Cosmos-behållare](how-to-provision-container-throughput.md).

* **Delade etablerat dataflöde** läge: De här behållarna dela det etablerade dataflödet som med andra behållare i samma databas (förutom de här behållarna som har konfigurerats med dedikerad etablerat dataflöde). Med andra ord delas dataflöde i databasen mellan alla ”delade dataflöde”-behållare. Mer information finns i [så här konfigurerar du etablerat dataflöde på en Azure Cosmos-databas](how-to-provision-database-throughput.md).

En Azure Cosmos-behållare kan Elastiskt skala, oavsett om du skapar behållare med antingen ”delade” eller ”dedikerad” etablerat dataflöde lägen.

En Azure Cosmos-behållare är en schemaoberoende behållare för objekt. Objekt i en behållare kan ha valfri scheman. Till exempel ett objekt som representerar en person, ett objekt som representerar en bil kan placeras i den *samma behållare*. Som standard indexera alla objekt som du lägger till en behållare automatiskt utan att kräva något explicit index eller schemahantering. Du kan anpassa beteendet indexering genom att konfigurera den [indexeringspolicy](index-overview.md) i en behållare. 

Du kan ange [Time To Live (TTL)](time-to-live.md) för markerade objekt i en Azure Cosmos-behållare eller för hela behållaren att smidigt rensa de objekt från systemet. Azure Cosmos DB kommer automatiskt att ta bort objekten när de går ut. Det garanterar även att inte returnerar en fråga som utförs på behållaren har upphört att gälla objekt i en fast gräns. Mer information finns i [hur du konfigurerar TTL på din behållare](how-to-time-to-live.md).

Med hjälp av [Change Feed](change-feed.md), du kan prenumerera åtgärdsloggen som hanteras för var och en av de logiska partitionerna för dina behållare. Change Feed innehåller en logg för alla uppdateringar som utförs på behållaren tillsammans med de före och efter-bilder av objekten. Se [skapa reaktiva program med hjälp av Change Feed](serverless-computing-database.md). Du kan också konfigurera kvarhållning varaktigheten för ändra Feed med ändringsflödet i principen för behållaren. 

Du kan registrera [lagrade procedurer, utlösare, användardefinierade funktioner (UDF)](stored-procedures-triggers-udfs.md) och [sammanfoga procedurer](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy-with-a-stored-procedure) med din Azure Cosmos-behållare. 

Du kan ange en [unika nyckelvillkor](unique-keys.md) på din Azure Cosmos-behållare. Genom att skapa en unik nyckel princip kan kontrollera du unikhet för ett eller flera värden per logisk partitionsnyckel. När du har skapat en behållare med en unik nyckel princip förhindrar skapande av alla nya eller uppdaterade objekt med värden som duplicerar värdena som anges av den unika key-begränsningen. Mer information finns i [unika nyckelvillkor](unique-keys.md).

En Azure Cosmos-behållare är specialiserat till API-specifika entiteter på följande sätt:

| **Azure Cosmos-enhet** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-behållare | Samling | Tabell | Samling | Graph | Tabell |

### <a name="properties-of-an-azure-cosmos-container"></a>Egenskaperna för en Azure Cosmos-behållare

En Azure Cosmos-behållare har en uppsättning systemdefinierade egenskaper. Beroende på valet av API: et, kan några av dem inte exponeras direkt. I följande tabell beskrivs i listan över systemdefinierade egenskaper:

| **Systemdefinierade egenskapen** | **Datorer som genererade eller användarangiven** | **Syfte** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | Systemgenererad | Unik identifierare för behållare | Ja | Nej | Nej | Nej | Nej |
|_etag | Systemgenererad | Enhetstagg som används för optimistisk samtidighetskontroll | Ja | Nej | Nej | Nej | Nej |
|_ts | Systemgenererad | Senast uppdaterade tidsstämpeln i behållaren | Ja | Nej | Nej | Nej | Nej |
|_self | Systemgenererad | Adresserbara URI: N för behållaren | Ja | Nej | Nej | Nej | Nej |
|id | Konfigureras av användaren | Användardefinierade unika namnet på behållaren | Ja | Ja | Ja | Ja | Ja |
|indexingPolicy | Konfigureras av användaren | Ger möjlighet att ändra index sökväg, Indextyp och indexläge. | Ja | Nej | Nej | Nej | Ja |
|TimeToLive | Konfigureras av användaren | Ger möjlighet att ta bort objekt automatiskt från en behållare efter en viss tidsperiod. Mer information finns i den [Time To Live](time-to-live.md) artikeln. | Ja | Nej | Nej | Nej | Ja |
|changeFeedPolicy | Konfigureras av användaren | Används för att läsa ändrade objekt i en behållare. Mer information finns i den [Change Feed](change-feed.md) artikeln. | Ja | Nej | Nej | Nej | Ja |
|uniqueKeyPolicy | Konfigureras av användaren | Används för att se till att en eller flera värden i en logisk partition är unikt. Mer information finns i den [unika Key-begränsningar](unique-keys.md) artikeln. | Ja | Nej | Nej | Nej | Ja |

### <a name="operations-on-an-azure-cosmos-container"></a>Åtgärder på en Azure Cosmos-behållare

En Azure Cosmos-behållare har stöd för följande åtgärder med hjälp av någon av Azure Cosmos-API: er.

| **Åtgärd** | **Azure CLI** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| Räkna upp behållare i en databas | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |
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

|**Systemdefinierade egenskapen** | **Datorer som genererade eller användarangiven**| **Syfte** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_id | Systemgenererad | Unik identifierare för objektet | Ja | Nej | Nej | Nej | Nej |
|_etag | Systemgenererad | Enhetstagg som används för optimistisk samtidighetskontroll | Ja | Nej | Nej | Nej | Nej |
|_ts | Systemgenererad | Tidsstämpel för senaste uppdateringen av objektet | Ja | Nej | Nej | Nej | Nej |
|_self | Systemgenererad | Adresserbara URI: N för objektet | Ja | Nej | Nej | Nej | Nej |
|id | Antingen | Användardefinierade unikt namn inom en logisk partition. Om användaren inte anger id, systemet automatiskt att generera en. | Ja | Ja | Ja | Ja | Ja |
|Godtycklig användardefinierade egenskaper | Användardefinierade | Användardefinierade egenskaper som visas i API-intern representation (JSON, BSON, CQL osv.) | Ja | Ja | Ja | Ja | Ja |

### <a name="operations-on-items"></a>Åtgärder på objekt

Azure Cosmos-objektet har stöd för följande åtgärder som kan utföras med hjälp av någon av Azure Cosmos-API: er.

| **Åtgärd** | **Azure CLI** | **SQL-API** | **API för Cassandra** | **Azure Cosmos DB: s API för MongoDB** | **Gremlin-API** | **Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| Infoga, Ersätt, ta bort, Upsert, Läs | Nej | Ja | Ja | Ja | Ja | Ja |

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta att lära dig följande begrepp:

* [Så här konfigurerar du etablerat dataflöde på en Azure Cosmos-databas](how-to-provision-database-throughput.md)
* [Så här konfigurerar du etablerat dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md)
* [Logiska partitioner](partition-data.md)
* [Så här konfigurerar du TTL för Azure Cosmos-behållare](how-to-time-to-live.md)
* [Skapa reaktiva program med hjälp av Change Feed](change-feed.md)
* [Så här konfigurerar du unika nyckelrestriktion i din Azure Cosmos-behållare](unique-keys.md)
