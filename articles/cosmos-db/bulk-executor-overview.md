---
title: Azure DB Cosmos bulk utföraren biblioteket – översikt | Microsoft Docs
description: Läs mer om Azure Cosmos DB bulk utföraren bibliotek, fördelarna med att använda biblioteket och dess arkitektur.
keywords: Java bulk utförare
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ramkris
ms.openlocfilehash: 7c490aa958cf9e78c260dd0fbcf7952b55d8d88c
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096185"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure DB Cosmos bulk utföraren biblioteket – översikt
 
Azure Cosmos-DB är en databastjänst för snabb, flexibel och globalt distribuerad som är designade Elastiskt skalbar att stöda: 

* Stor läsa och skriva genomflöden (miljontals åtgärder per sekund).  
* Lagra stora mängder (hundratals terabyte eller ännu mer) transaktionella och operational data med förutsägbar millisekunds fördröjning.  

Massinläsning utföraren biblioteket kan du utnyttja den här omfattande genomströmning och lagring, bulk utföraren biblioteket kan du utföra bulk åtgärder i Azure Cosmos DB via massimport och bulk uppdatera API: er. Du kan läsa mer om funktionerna i bulk utföraren biblioteket i följande avsnitt. 

> [!NOTE] 
> För närvarande bulk utföraren bibliotek stöder import och uppdateringsåtgärder och det här biblioteket stöds av Azure SQL DB-API Cosmos-konton. Se [.NET](sql-api-sdk-bulk-executor-dot-net.md) och [Java](sql-api-sdk-bulk-executor-java.md) viktig information om eventuella uppdateringar i biblioteket.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Viktiga funktioner i bulk utföraren biblioteket  
 
* Det minskar avsevärt klientsidan beräkningsresurser som behövs för att fylla genomströmning som allokerats till en behållare. Ett enda flertrådade program som skriver data med hjälp av bulk import API uppnår 10 gånger större genomströmning för skrivning jämfört med ett flertrådat program som skriver data parallellt medan att överbelastas klienten datorns processor.  

* Den avlägsnar direkt tråkigt uppgifter för att skriva programlogiken till att hantera hastighetsbegränsning begäran, begäran-timeout och andra tillfälligt undantag genom att effektivt hantera dem i biblioteket.  

* Det ger en förenklad mekanism för program som utför massåtgärder ska skalas ut. En enda grupp utföraren-instans som körs på en Azure VM kan använda större än 500 K RU/s och du kan uppnå högre hastighet för dataflödet genom att lägga till ytterligare instanser på enskilda klienten virtuella datorer.  
 
* Det kan massimportera importera fler än en terabyte data inom en timme med hjälp av en skalbar arkitektur.  

* Det kan massimportera uppdatera befintliga data i Azure DB som Cosmos-behållare som korrigeringar. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hur fungerar Bulk utföraren? 

När en massåtgärd att importera eller uppdatera dokument utlöses med en batch med entiteter blandad ursprungligen i buckets som motsvarar deras Azure Cosmos DB partitionsnyckelintervallet. Inom varje bucket som motsvarar en partitionsnyckelintervallet är de uppdelad i mini-batchar och varje Mini batch agera som en nyttolast som har genomförts på serversidan. Massinläsning utföraren biblioteket har inbyggda optimeringar för samtidig körning av dessa mini-batchar både inom och över partition nyckelintervall. Följande bild illustrerar hur bulk utföraren batchar data till olika partitionsnycklar:  

![Massinläsning utföraren arkitektur](./media/bulk-executor-overview/bulk-executor-architecture.png)

Bulk utföraren biblioteket ser till att använda högst genomströmning som allokerats till en samling. Den använder en [AIMD-format överbelastning kontrollen mekanism](https://tools.ietf.org/html/rfc5681) partitions viktiga intervallet att effektivt hantera hastighetsbegränsning och timeout för varje Azure Cosmos-DB. 

## <a name="next-steps"></a>Nästa steg 
  
* Lär dig mer av provat exempelprogrammen förbrukar Bulk utföraren biblioteket i [.NET](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md).  
* Kolla in bulk utföraren SDK information och version anteckningarna i [.NET](sql-api-sdk-bulk-executor-dot-net.md) och [Java](sql-api-sdk-bulk-executor-java.md).
* Massinläsning utföraren biblioteket är integrerat i Cosmos DB Spark-kopplingen, Läs mer i [Azure Cosmos DB Spark connector](spark-connector.md) artikel.  
* Massinläsning utföraren biblioteket också integreras i en ny version av [Azure Cosmos DB connector](https://aka.ms/bulkexecutor-adf-v2) för Azure Data Factory för att kopiera data.
