---
title: "JSON-utdata för Stream Analytics | Microsoft Docs"
description: "Lär dig hur Stream Analytics kan inrikta dig på Azure Cosmos DB för JSON-utdata för dataarkivering och låg latens frågor för Ostrukturerade JSON-data."
keywords: JSON-utdata
documentationcenter: 
services: stream-analytics,documentdb
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: cc80b0080c806541362a1ef2d71b95862bd51ca2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="target-azure-cosmos-db-for-json-output-from-stream-analytics"></a>Mål Azure Cosmos DB för JSON-utdata från Stream Analytics
Stream Analytics kan rikta [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) att aktivera arkivering och låg latens datafrågor på Ostrukturerade JSON-data för JSON-utdata. Det här dokumentet beskrivs några metoder för att implementera den här konfigurationen.

För de som inte är bekant med Cosmos DB, ta en titt på [Azure Cosmos DB Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/documentdb/) att komma igång. 

Obs: Mongo DB API-baserad Cosmos DB samlingar stöds inte för närvarande. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Grunderna i Cosmos DB som ett mål för utdata
Azure DB som Cosmos-utdata i Stream Analytics kan skriva strömmen bearbetning resultatet som JSON-utdata till Cosmos-DB-samling(ar). Stream Analytics skapar inte samlingar i databasen, i stället att du behöver skapa dem på en gång. Detta är så att fakturering kostnaderna för Cosmos DB samlingar är transparent och så att du kan finjustera prestanda, konsekvens och kapacitet för samlingar direkt med den [Cosmos DB-API: er](https://msdn.microsoft.com/library/azure/dn781481.aspx). Vi rekommenderar en Cosmos-DB-databas per direktuppspelningsjobbet till logiskt separata samlingar för ett direktuppspelningsjobb.

Cosmos DB samling alternativen beskrivs nedan.

## <a name="tune-consistency-availability-and-latency"></a>Finjustera konsekvens, tillgänglighet och svarstid
Om du vill matcha ditt programkrav kan Cosmos DB du finjustera databasen och samlingar och kompromissa mellan konsekvens, tillgänglighet och svarstid. Beroende på vilka nivåer av läskonsekvens scenariot behov mot läsa och skriva latens, du kan välja en konsekvensnivå på ditt konto. Som standard kan Cosmos DB också synkron indexering på varje CRUD-åtgärd i din samling. Det här är ett annat bra alternativ för att ange skrivning/läsning prestanda i Cosmos-databasen. Ytterligare information om det här avsnittet finns det [ändra din databas och fråga konsekvensnivåer](../documentdb/documentdb-consistency-levels.md) artikel.

## <a name="upserts-from-stream-analytics"></a>Upserts från Stream Analytics
Stream Analytics-integrering med Cosmos DB kan du infoga eller uppdatera poster i din Cosmos-DB-samling baserat på en viss dokument-ID-kolumn. Detta kallas även för som en *Upsert*.

Stream Analytics använder optimistisk Upsert lösning där uppdateringar görs endast när insert misslyckas på grund av ett dokument-ID-konflikter. Den här uppdateringen utförs av Stream Analytics som en korrigering, så gör deluppdateringar till dokument, t.ex. lägga till nya egenskaper eller ersätta en befintlig egenskap utförs inkrementellt. Observera att ändringar av värdena i matrisen egenskaper i JSON-dokument resultatet i hela matrisen komma över, d.v.s. matrisen samman inte.

## <a name="data-partitioning-in-cosmos-db"></a>Datapartitionering i Cosmos-DB
Cosmos DB [partitionerade samlingar](../cosmos-db/partition-data.md) är den rekommenderade metoden för partitionering av dina data. 

För enkel Cosmos DB samlingar kan Stream Analytics du fortfarande partitionera data baserat på både den frågemönster och prestanda för programmet. Varje samling kan innehålla upp till 10GB data (max) och det finns för närvarande inget sätt att skala upp (eller dataspill) en samling. För att skala ut Stream Analytics gör att du kan skriva till flera samlingar med ett givet prefix (se användningsinformation nedan). Stream Analytics använder det konsekvent [hash-Partition matcharen](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) strategi baserat på användaren som PartitionKey kolumnen partitionera dess utdata-poster. Antal samlingar med det angivna prefixet vid strömning jobbets starttid används som utdata antalet partitioner, som jobbet skriver till parallellt (Cosmos DB samlingar = utdata partitioner). För en enda samling med lazy indexering detta endast infogar, om 0,4 som kan förväntas MB/s genomströmning för skrivning. Med hjälp av flera samlingar kan du uppnå högre genomflöde och bättre kapacitet.

Om du vill öka antalet partitioner i framtiden, kan du behöva stoppa jobbet, där data från din befintliga samlingar till nya samlingar och starta sedan om Stream Analytics-jobbet. Mer information om att använda PartitionResolver och nytt partitionering tillsammans med exempelkod inkluderas i en e-post. Artikeln [partitionering och skalning i Cosmos DB](../documentdb/documentdb-partition-data.md) innehåller även information om detta.

## <a name="cosmos-db-settings-for-json-output"></a>Cosmos DB inställningar för JSON-utdata
Skapa Cosmos DB som utdata i Stream Analytics genererar en fråga om information som visas nedan. Det här avsnittet innehåller en förklaring av egenskaper för definition.

Partitionerad samling | Flera ”enskild Partition” samlingar
---|---
![documentdb stream analytics utdata skärmen](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![documentdb stream analytics utdata skärmen](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> Den **flera ”enkel” partitionssamlingar** scenariot kräver en partitionsnyckel och är en konfiguration som stöds. 

* **Utdata Alias** – ett alias att referera den här utdata i frågan ASA  
* **Kontonamn** – namn eller endpoint-URI för Cosmos-DB-konto.  
* **Kontot nyckeln** – den delade åtkomstnyckeln för kontot Cosmos DB.  
* **Databasen** – det Cosmos-DB-databasnamn.  
* **Samlingsnamnsmönstret** – namnet på samlingen eller deras mönster för samlingar som ska användas. Samlingsnamnsformatet kan konstrueras med valfritt {partition}-token, där partitionerna börjar från 0. Följande är exempel giltiga indata:  
  1\) MyCollection – en samling med namnet ”MyCollection” måste finnas.  
  2\) MyCollection {partition} – dessa samlingar måste finnas – ”MyCollection0”, ”MyCollection1”, ”MyCollection2” och så vidare.  
* **Partitionera nyckeln** – det är valfritt. Det här krävs bara om du använder en {partition}-token i din samlingsnamnsmönstret. Namnet på fältet i utdatahändelserna används för att specificera nyckeln för att partionera utdata över samlingarna. Enda samling utdata för en godtycklig utdatakolumnen kan vara används t.ex. PartitionId.  
* **Dokumentera ID** – det är valfritt. Namnet på fältet i utdatahändelserna används för att ange den primära nyckeln operations baseras på vilka insert eller update.  
