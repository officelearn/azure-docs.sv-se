---
title: Åtkomst till ändrings flöde i Azure Cosmos DB Azure Cosmos DB
description: I den här artikeln beskrivs olika alternativ som är tillgängliga för läsning och åtkomst av ändrings flöden i Azure Cosmos DB Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688118"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Läser Azure Cosmos DB ändra feed

Du kan arbeta med Azure Cosmos DB ändra feed med något av följande alternativ:

* Använda Azure Functions
* Använda biblioteket Change feed processor
* Använda Azure Cosmos DB SQL API SDK

## <a name="using-azure-functions"></a>Använda Azure Functions

Azure Functions är det enklaste och rekommenderade alternativet. När du skapar en Azure Functions-utlösare för Cosmos DB, kan du välja den behållare som ska anslutas och Azure-funktionen utlöses när det finns en ändring i behållaren. Utlösare kan skapas med hjälp av Azure Functions Portal, Azure Cosmos DB portalen eller via programmering med SDK: er. Visual Studio och VS Code ger stöd för att skriva Azure Functions, och du kan även använda Azure Functions CLI för utveckling över plattformar. Du kan skriva och felsöka koden på Skriv bordet och sedan distribuera funktionen med ett klick. Mer information finns i artiklar om [data behandling utan server med hjälp av Azure Functions](serverless-computing-database.md) och [använda ändra feed med Azure Functions](change-feed-functions.md)).

## <a name="using-the-change-feed-processor-library"></a>Använda biblioteket Change feed processor

I biblioteket för ändrings matnings processor döljs komplexitet och du får fortfarande en fullständig kontroll över ändrings flödet. Biblioteket följer det observatörs mönster där din bearbetnings funktion anropas av biblioteket. Om du har en hög data flödes ändrings flöde kan du instansiera flera klienter för att läsa ändrings flödet. Eftersom du använder ändra flödes processor bibliotek delar den automatiskt in belastningen mellan olika klienter utan att du behöver implementera den här logiken. All komplexitet hanteras av biblioteket. Om du vill använda en egen belastningsutjämnare kan du implementera `IPartitionLoadBalancingStrategy` för en anpassad partitionsnyckel för att bearbeta ändrings flödet. Läs mer i [använda Change feed processor-bibliotek](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Använda Azure Cosmos DB SQL API SDK

Med SDK får du en kontroll på låg nivå för ändrings flödet. Du kan hantera kontroll punkten, komma åt en viss logisk partitionsnyckel osv. Om du har flera läsare kan du använda `ChangeFeedOptions` för att distribuera Läs belastning till olika trådar eller olika klienter.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Ändra feed i API: er för Cassandra och MongoDB

Funktionen ändra feed fungerar som en ändrings ström i MongoDB API och fråga med predikat i API för Cassandra. Mer information om implementerings informationen för MongoDB-API finns i [ändrings strömmar i Azure Cosmos DB API för MongoDB](mongodb-change-streams.md).

Inbyggd Apache Cassandra tillhandahåller registrering av ändrings data (CDC), en mekanism för att flagga specifika tabeller för arkivering och avvisning av skrivningar till dessa tabeller när en konfigurerbar storlek-på disk för CDC-loggen nås. Funktionen ändra feed i Azure Cosmos DB API för Cassandra förbättrar möjligheten att fråga ändringarna med predikat via CQL. Mer information om implementerings informationen finns i [ändra feed i Azure Cosmos DB API för Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med att lära dig mer om ändrings flöden i följande artiklar:

* [Översikt över ändra feed](change-feed.md)
* [Använda ändra feed med Azure Functions](change-feed-functions.md)
* [Använda biblioteket Change feed processor](change-feed-processor.md)
