---
title: Komma åt ändringsflödet i Azure Cosmos DB Azure Cosmos DB
description: I den här artikeln beskrivs olika alternativ som är tillgängliga för att läsa och komma åt ändringsflödet i Azure Cosmos DB Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688118"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Läsa ändringsflödet i Azure Cosmos DB

Du kan arbeta med Azure Cosmos DB-ändringsflödet med något av följande alternativ:

* Använda Azure Functions
* Använda ändringsflödesprocessorbiblioteket
* Använda Azure Cosmos DB SQL API-SDK

## <a name="using-azure-functions"></a>Använda Azure Functions

Azure Functions är det enklaste och rekommenderade alternativet. När du skapar en Azure Functions-utlösare för Cosmos DB kan du välja behållaren att ansluta och Azure-funktionen utlöses när det sker en ändring av behållaren. Utlösare kan skapas med hjälp av Azure Functions-portalen, Azure Cosmos DB-portalen eller programmässigt med SDK:er. Visual Studio och VS-kod ger stöd för att skriva Azure-funktioner, och du kan även använda Azure Functions CLI för plattformsoberoende utveckling. Du kan skriva och felsöka koden på skrivbordet och sedan distribuera funktionen med ett klick. Se [Serverless databasdatoranvändning med hjälp av Azure Functions](serverless-computing-database.md) och Använda [ändringsflöde med Azure Functions](change-feed-functions.md)) artiklar om du vill veta mer.

## <a name="using-the-change-feed-processor-library"></a>Använda ändringsflödesprocessorbiblioteket

Ändringsflödesprocessorbiblioteket döljer komplexitet och ger dig fortfarande en fullständig kontroll över ändringsflödet. Biblioteket följer observatörsmönstret, där din bearbetningsfunktion anropas av biblioteket. Om du har en aktivitetsflöde med högt dataflöde kan du instansiera flera klienter för att läsa ändringsflödet. Eftersom du använder ändringsflödesprocessorbiblioteket delar du automatiskt upp belastningen mellan de olika klienterna utan att du behöver implementera den här logiken. All komplexitet hanteras av biblioteket. Om du vill ha en egen belastningsutjämnare kan du implementera `IPartitionLoadBalancingStrategy` en anpassad partitionsstrategi för att bearbeta ändringsflödet. Mer information finns [i använda ändringsflödesprocessorbiblioteket](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Använda Azure Cosmos DB SQL API-SDK

Med SDK får du en låg nivå kontroll över ändringsflödet. Du kan hantera kontrollpunkten, komma åt en viss logisk partitionsnyckel, etc. Om du har flera läsare `ChangeFeedOptions` kan du använda för att distribuera läsbelastning till olika trådar eller olika klienter.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Ändra feed i API:er för Cassandra och MongoDB

Ändra matningsfunktioner visas som ändringsström i MongoDB API och Fråga med predikat i Cassandra API. Mer information om implementeringsinformationen för MongoDB API finns [i Ändra strömmar i Azure Cosmos DB API för MongoDB](mongodb-change-streams.md).

Native Apache Cassandra ger förändring datainsamling (CDC), en mekanism för att flagga specifika tabeller för arkivering samt avvisa skrivningar till dessa tabeller när en konfigurerbar storlek-på-disk för CDC loggen nås. Ändringsfeedfunktionen i Azure Cosmos DB API för Cassandra förbättrar möjligheten att fråga ändringarna med predikat via CQL. Mer information om implementeringsinformationen finns [i Ändra feed i Azure Cosmos DB API för Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta med att läsa mer om ändringsflödet i följande artiklar:

* [Översikt över ändringsflödet](change-feed.md)
* [Använda ändringsfeed med Azure Functions](change-feed-functions.md)
* [Använda ändringsmatarprocessorbibliotek](change-feed-processor.md)
