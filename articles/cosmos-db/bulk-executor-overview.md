---
title: Massimport och uppdatera data i Azure Cosmos DB med hjälp av klientbiblioteket för bulk-executor | Microsoft Docs
description: 'Utföra massåtgärder i Azure Cosmos DB via massimport och Massuppdatering API: er som erbjuds av bulk executor biblioteket.'
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.openlocfilehash: a760de998c78ce2afdd24a15d9dd6e5d0cf44dc1
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363556"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB bulk executor biblioteket – översikt
 
Azure Cosmos DB är en snabb, flexibel och globalt distribuerad databastjänst som är utformat att Elastiskt skala ut med stöd: 

* Stor läser och skriver dataflöde (miljoner åtgärder per sekund).  
* Lagra stora mängder (hundratals terabyte eller även flera) transaktions- och data med förutsägbara millisekunds fördröjning.  

Massinläsning executor biblioteket kan du använda den här massivt dataflöde och lagring. Massinläsning executor biblioteket kan du utför åtgärder i Azure Cosmos DB via massimport och masskyddsåtgärder uppdatera API: er. Du kan läsa mer om funktionerna i bulk executor bibliotek i följande avsnitt. 

> [!NOTE] 
> För närvarande bulk executor bibliotek stöder import och uppdateringsåtgärder och det här biblioteket som stöds av endast Azure Cosmos DB SQL API och Gremlin-API-konton.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Viktiga funktioner i bulk executor biblioteket  
 
* Det minskar avsevärt klientsidan beräkningsresurser som behövs för att fylla det dataflöde som allokeras till en behållare. Ett enda flertrådade program som skriver data med hjälp av bulk importera API uppnår 10 gånger högre genomströmning för skrivning jämfört med ett flertrådiga program som skriver data parallellt vid överbelastas klienten datorns processor.  

* Den avlägsnar tedious uppgifter för att skriva programlogik som hanterar hastighetsbegränsning på begäran, timeout för begäran och andra tillfälliga undantag eftersom de effektivt hanterar dem i biblioteket.  

* Det ger en förenklad mekanism för program som utför massåtgärder att skala ut. En enda grupp executor-instans som körs på en virtuell Azure-dator kan använda större än 500 K RU/s och du kan få ett högre dataflöde kostnad genom att lägga till ytterligare instanser på enskilda klient-VM.  
 
* Det kan masstilldela importera fler än en terabyte data inom en timme med hjälp av en skalbar arkitektur.  

* Det kan masstilldela uppdatera befintliga data i Azure Cosmos DB-behållare som korrigeringar. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Hur fungerar Bulk-Executor? 

När en bulkåtgärd att importera eller uppdatera dokument utlöses med en batch med entiteter, blandad inledningsvis till buckets som motsvarar deras partitionsnyckelintervall för Azure Cosmos DB. Inom varje bucket som motsvarar en partitionsnyckelintervall, kan de delas upp i mini-batchar och varje act Mini batch som en nyttolast som har genomförts på serversidan. Massinläsning executor biblioteket har inbyggda optimeringar för samtidig körning av dessa mini-batchar både inom och mellan olika partition nyckelintervall. Följande bild illustrerar hur bulk executor slår ihop data till olika partitionsnycklar:  

![Massinläsning executor arkitektur](./media/bulk-executor-overview/bulk-executor-architecture.png)

Bulk Executor biblioteket ser till att använda högst dataflödet som allokerats till en samling. Den använder en [AIMD-style överbelastning mekanismen för multifaktoråtkomstkontroll](https://tools.ietf.org/html/rfc5681) partitionera nyckelintervall att effektivt hantera hastighetsbegränsning och tidsgränser för varje Azure Cosmos DB. 

## <a name="next-steps"></a>Nästa steg 
  
* Lär dig mer genom att prova exempelprogrammen förbrukar Bulk Executor biblioteket i [.NET](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md).  
* Kolla in de bulk executor SDK information och viktig information i [.NET](sql-api-sdk-bulk-executor-dot-net.md) och [Java](sql-api-sdk-bulk-executor-java.md).
* Massinläsning Executor biblioteket är integrerad i Cosmos DB Spark connector, mer information finns i [Azure Cosmos DB Spark connector](spark-connector.md) artikeln.  
* Massinläsning executor biblioteket är också integrerat i en ny version av [Azure Cosmos DB-anslutningsapp](https://aka.ms/bulkexecutor-adf-v2) för Azure Data Factory att kopiera data.
