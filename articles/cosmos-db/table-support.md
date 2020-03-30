---
title: Azure Table Storage-stöd i Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB Table API och Azure Storage Tables fungerar tillsammans genom att dela samma tabelldatamodell en verksamhet
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 12/02/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 82397e49e473b8660dfada54a0e05cafe0da4120
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76770669"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Utveckla med Azure Cosmos DB Table API:t och Azure Table Storage

Azure Cosmos DB Table API:t och Azure Table Storage delar samma tabelldatamodell och exponerar samma åtgärder för att skapa, ta bort, uppdatera och fråga via sina SDK:er.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Utveckla med Azure Cosmos DB Table API:t

Just nu har [Azure Cosmos DB Table API:t](table-introduction.md) fyra SDK:er tillgängliga för utveckling: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Det här biblioteket har NET Standard som mål och har samma klasser och metodsignaturer som den offentliga versionen av [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), men har även funktioner för att ansluta till Azure Cosmos DB-konton via Tabell-API. Användare av .NET Framework-biblioteket [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) rekommenderas att uppgradera till [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) eftersom det är i underhållsläge och kommer att vara inaktuella snart.

* [Python SDK:](table-sdk-python.md)Den nya Azure Cosmos DB Python SDK är den enda SDK som stöder Azure Table-lagring i Python. Detta SDK fungerar med både Azure Table Storage och Azure Cosmos DB Table API:t.

* [Java SDK](table-sdk-java.md): Den här Azure Storage SDK har möjlighet att ansluta till Azure Cosmos DB-konton med tabell-API:et.

* [Node.js SDK](table-sdk-nodejs.md): Den här Azure Storage SDK har möjlighet att ansluta till Azure Cosmos DB-konton med tabell-API:et.


Ytterligare information om hur du arbetar med Table API:t finns i artikeln [Vanliga frågor och svar: utveckla med Table API](faq.md#table).

## <a name="developing-with-azure-table-storage"></a>Utveckla med Azure Table Storage

Azure Table storage har de här SDK:erna tillgängliga för utveckling:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Det här biblioteket låter dig arbeta med lagringstabelltjänsten.
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). Azure Cosmos DB Table SDK för Python stöder tjänsten Table Storage (eftersom Azure Table Storage och Cosmos DB:s tabell-API har samma funktioner och i ett försök att ta hänsyn till våra SDK-utvecklingsinsatser rekommenderar vi att du använder den här SDK).
- [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java). Den här Azure Storage SDK:n tillhandahåller ett klientbibliotek i Java för att använda Azure Table-lagring.
- [Nod.js SDK](https://github.com/Azure/azure-storage-node). Detta SDK erbjuder ett Node.js-paket och ett webbläsarkompatibelt JavaScript-klientbiblioteket för att använda lagringstabelltjänsten.
- [AzureRmStorageTable PowerShell-modulen](https://www.powershellgallery.com/packages/AzureRmStorageTable). Den här PowerShell-modulen har cmdletar för att arbeta med lagringstabeller.
- [Azure Storage-klientbibliotek för C++](https://github.com/Azure/azure-storage-cpp/). Det här biblioteket låter dig skapa program mot Azure Storage.
- [Azure Storage Table-klientbiblioteket för Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Det här projektet tillhandahåller ett Ruby-paket som gör det enkelt att komma åt Azure Table-lagringstjänster.
- [Azure Storage Table PHP-klientbibliotek](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Det här projektet tillhandahåller ett PHP-klientbibliotek som gör det enkelt att komma åt Azure Table-lagringstjänster.


   





