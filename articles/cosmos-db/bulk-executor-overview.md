---
title: Azure Cosmos DB BulkExecutor biblioteket – översikt | Microsoft Docs
description: Läs mer om Azure Cosmos DB BulkExecutor bibliotek, fördelarna med att använda biblioteket och dess arkitektur.
keywords: Java bulk utförare
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 5f1987009d2277590e32136336340aa1b3be07fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610352"
---
# <a name="azure-cosmos-db-bulkexecutor-library-overview"></a>Azure Cosmos DB BulkExecutor biblioteket – översikt
 
Azure Cosmos-DB är en databastjänst för snabb, flexibel och globalt distribuerad som är designade Elastiskt skalbar att stöda: 

* Stor läsa och skriva genomflöden (miljontals åtgärder per sekund).  
* Lagra stora mängder (hundratals terabyte eller ännu mer) transaktionella och operational data med förutsägbar millisekunds fördröjning.  

BulkExecutor-biblioteket kan du utnyttja den här omfattande genomströmning och lagring, det BulkExecutor biblioteket kan du utföra bulk åtgärder i Azure Cosmos DB via massimport och bulk uppdatera API: er. Du kan läsa mer om funktionerna i BulkExecutor bibliotek i följande avsnitt. 

> [!NOTE] 
> För närvarande Bulkxecutor bibliotek stöder import och uppdateringsåtgärder och det här biblioteket stöds av Azure SQL DB-API Cosmos-konton. Se [.NET](sql-api-sdk-bulk-executor-dot-net.md) och [Java](sql-api-sdk-bulk-executor-java.md) viktig information om eventuella uppdateringar i biblioteket.
 
## <a name="key-features-of-the-bulkexecutor-library"></a>Viktiga funktioner i BulkExecutor-biblioteket  
 
* Det minskar avsevärt klientsidan beräkningsresurser som behövs för att fylla genomströmning som allokerats till en behållare. Ett enda flertrådade program som skriver data med hjälp av bulk import API uppnår 10 gånger större genomströmning för skrivning jämfört med ett flertrådat program som skriver data parallellt medan att överbelastas klienten datorns processor.  

* Den avlägsnar direkt tråkigt uppgifter för att skriva programlogiken till att hantera begäran begränsning begäran-timeout och andra tillfälligt undantag genom att effektivt hantera dem i biblioteket.  

* Det ger en förenklad mekanism för program som utför massåtgärder ska skalas ut. En enda BulkExecutor-instans som körs på en Azure VM kan använda större än 500 K RU/s och du kan uppnå högre hastighet för dataflödet genom att lägga till ytterligare instanser på enskilda klienten virtuella datorer.  
 
* Det kan massimportera importera fler än en terabyte data inom en timme med hjälp av en skalbar arkitektur.  

* Det kan massimportera uppdatera befintliga data i Azure DB som Cosmos-behållare som korrigeringar. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hur fungerar Bulk utföraren? 

När en massåtgärd att importera eller uppdatera dokument utlöses med en batch med entiteter blandad ursprungligen i buckets som motsvarar deras Azure Cosmos DB partitionsnyckelintervallet. Inom varje bucket som motsvarar en partitionsnyckelintervallet är de uppdelad i mini-batchar och varje Mini batch agera som en nyttolast som har genomförts på serversidan. BulkExecutor biblioteket har inbyggda optimeringar för samtidig körning av dessa mini-batchar både inom och över partition nyckelintervall. Följande bild illustrerar hur BulkExecutory batchar data till olika partitionsnycklar:  

![Massinläsning utföraren arkitektur](./media/bulk-executor-overview/bulk-executor-architecture.png)

Bulk utföraren biblioteket ser till att använda högst genomströmning som allokerats till en samling. Den använder en [AIMD-format överbelastning kontrollen mekanism](https://tools.ietf.org/html/rfc5681) för varje Azure Cosmos DB partitions viktiga intervallet att effektivt hantera begränsning och timeout. 

## <a name="next-steps"></a>Nästa steg 
  
* Lär dig mer av provat exempelprogrammen förbrukar Bulk utföraren biblioteket i [.NET](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md).  
* Checka ut BulkExecutor SDK information och viktig information i [.NET](sql-api-sdk-bulk-executor-dot-net.md) och [Java](sql-api-sdk-bulk-executor-java.md).
* Massinläsning utföraren biblioteket är integrerat i Cosmos DB Spark-kopplingen, Läs mer i [Azure Cosmos DB Spark connector](spark-connector.md) artikel.  
* Biblioteket BulkExecutor också integreras i en ny version av [Azure Cosmos DB connector](https://aka.ms/bulkexecutor-adf-v2) för Azure Data Factory för att kopiera data.
