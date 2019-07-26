---
title: Åtkomst till ändrings flöde i Azure Cosmos DB Azure Cosmos DB
description: I den här artikeln beskrivs olika alternativ som är tillgängliga för läsning och åtkomst av ändrings flöden i Azure Cosmos DB Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: 3d52ba1abc22aae6121ea6a36f943851dfcca7a0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467668"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Läser Azure Cosmos DB ändra feed

Du kan arbeta med Azure Cosmos DB ändra feed med något av följande alternativ:

* Använda Azure Functions
* Använda biblioteket Change feed processor
* Använda Azure Cosmos DB SQL API SDK

## <a name="using-azure-functions"></a>Använda Azure Functions

Azure Functions är det enklaste och rekommenderade alternativet. När du skapar en Azure Functions-utlösare för Cosmos DB, kan du välja den behållare som ska anslutas och Azure-funktionen utlöses när det finns en ändring i behållaren. Utlösare kan skapas med hjälp av Azure Functions Portal, Azure Cosmos DB portalen eller via programmering med SDK: er. Visual Studio och VS Code ger stöd för att skriva Azure Functions, och du kan även använda Azure Functions CLI för utveckling över plattformar. Du kan skriva och felsöka koden på Skriv bordet och sedan distribuera funktionen med ett klick. Mer information finns i artiklar om [data behandling utan server med hjälp av Azure Functions](serverless-computing-database.md) och [använda ändra feed med Azure Functions](change-feed-functions.md)).

## <a name="using-the-change-feed-processor-library"></a>Använda biblioteket Change feed processor

I biblioteket för ändrings matnings processor döljs komplexitet och du får fortfarande en fullständig kontroll över ändrings flödet. Biblioteket följer det observatörs mönster där din bearbetnings funktion anropas av biblioteket. Om du har en hög data flödes ändrings flöde kan du instansiera flera klienter för att läsa ändrings flödet. Eftersom du använder ändra flödes processor bibliotek delar den automatiskt in belastningen mellan olika klienter utan att du behöver implementera den här logiken. All komplexitet hanteras av biblioteket. Om du vill använda en egen belastningsutjämnare kan du implementera `IPartitionLoadBalancingStrategy` en anpassad partitionsnyckel för att bearbeta ändrings flödet. Läs mer i [använda Change feed processor-bibliotek](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Använda Azure Cosmos DB SQL API SDK

Med SDK får du en kontroll på låg nivå för ändrings flödet. Du kan hantera kontroll punkten, komma åt en viss logisk partitionsnyckel osv. Om du har flera läsare kan du använda `ChangeFeedOptions` för att distribuera Läs belastning till olika trådar eller olika klienter. 

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta att lära dig mer om ändringsfeed i följande artiklar:

* [Översikt över ändra feed](change-feed.md)
* [Med hjälp av ändringen feed med Azure Functions](change-feed-functions.md)
* [Med hjälp av ändringen feed processor-biblioteket](change-feed-processor.md)
