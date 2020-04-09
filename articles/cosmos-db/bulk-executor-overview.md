---
title: Översikt över massexecutor-biblioteket i Azure Cosmos DB
description: Utför massåtgärder i Azure Cosmos DB via massimport- och massuppdaterings-API:er som erbjuds av massutflödesbiblioteket.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: af17f9c2ef7eea5eb531327d4df13d5885a49b7e
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985600"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Översikt över massexecutor-biblioteket i Azure Cosmos DB
 
Azure Cosmos DB är en snabb, flexibel och globalt distribuerad databastjänst som är utformad för att elastiskt skala ut och stödja: 

* Stora läs- och skrivdataflöden (flera miljoner åtgärder per sekund).  
* Lagra stora mängder (hundratals terabyte eller mer) transaktions- och åtgärdsdata med förutsägbara millisekundssvarstider.  

Massexecutor-biblioteket hjälper dig att dra nytta av det omfattande dataflödet och lagringsutrymmet. Med massexecutor-biblioteket kan du utföra massåtgärder i Azure Cosmos DB med hjälp av API:er för massimport och massuppdatering. Du kan läsa mer om funktionerna i massexecutor-biblioteket i följande avsnitt. 

> [!NOTE] 
> För närvarande stöder massutnrevarbiblioteket import- och uppdateringsåtgärder och det här biblioteket stöds endast av Azure Cosmos DB SQL API- och Gremlin API-konton.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Viktiga funktioner i massutförandeörsbiblioteket  
 
* Det minskar avsevärt beräkningsresurser på klientsidan som behövs för att mätta dataflödet som allokerats till en behållare. Ett enda trådat program som skriver data med massimport-API:et uppnår 10 gånger större skrivdataflöde jämfört med ett flertrådat program som skriver data parallellt samtidigt som klientdatorns PROCESSOR mättas.  

* Det abstraherar bort tråkiga uppgifter att skriva programlogik för att hantera hastighetsbegränsning av begäranden, tidsgränsen för begäran och andra tillfälliga undantag genom att effektivt hantera dem i biblioteket.  

* Det ger en förenklad mekanism för program som utför massåtgärder för att skala ut. En enda masskörningsklient som körs på en Azure VM kan förbruka mer än 500 000 RU/s och du kan uppnå en högre dataflödeshastighet genom att lägga till ytterligare instanser på enskilda klient-virtuella datorer.  
 
* Det kan massimportera mer än en terabyte data inom en timme med hjälp av en skalningsarkitektur.  

* Det kan massuppdatering av befintliga data i Azure Cosmos-behållare som korrigeringsfiler. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hur fungerar bulk executor? 

När en massåtgärd för att importera eller uppdatera dokument utlöses med en batch med entiteter, blandas de inledningsvis i buckets som motsvarar deras Azure Cosmos DB-partitionsnyckelintervall. Inom varje bucket som motsvarar ett partitionsnyckelintervall delas de upp i minibatch och varje minibatch fungerar som en nyttolast som har bekräftats på serversidan. Bulk executor biblioteket har inbyggda optimeringar för samtidig körning av dessa mini-batchar både inom och över partitionen nyckelintervall. Följande bild illustrerar hur bulk executor batchar data till olika partitionsnycklar:  

![Massutringsarkitektarkitektur](./media/bulk-executor-overview/bulk-executor-architecture.png)

Bulk executor biblioteket ser till att maximalt utnyttja dataflödet som tilldelats en samling. Den använder en [AIMD-stil överbelastningskontroll mekanism](https://tools.ietf.org/html/rfc5681) för varje Azure Cosmos DB partition nyckelintervall för att effektivt hantera hastighetsbegränsning och timeouts. 

## <a name="next-steps"></a>Efterföljande moment 
  
* Läs mer genom att prova exempelprogrammen som använder massutdrivarbiblioteket i [.NET](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md).  
* Kolla in massutn0-körorn SDK-information och viktig information i [.NET](sql-api-sdk-bulk-executor-dot-net.md) och [Java](sql-api-sdk-bulk-executor-java.md).
* Massutnämningsbiblioteket är integrerat i Cosmos DB Spark-kopplingen, mer information finns i azure [cosmos DB Spark-anslutningsartikeln.](spark-connector.md)  
* Massutnämningsbiblioteket är också integrerat i en ny version av [Azure Cosmos DB-anslutning](../data-factory/connector-azure-cosmos-db.md) för Azure Data Factory för att kopiera data.
