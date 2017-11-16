---
title: "Azure Table Storage-stöd i Azure Cosmos DB | Microsoft Docs"
description: "Lär dig hur Azure Cosmos DB tabell API och Azure Storage-tabeller tillsammans."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: c0c8f1aee75c4ee5cc35758b71ef573637fd3edd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Utveckla med Azure Cosmos DB tabell API och Azure Table storage

Azure Cosmos DB tabell API och Azure Table storage delar samma tabell datamodellen och exponera samma skapa, ta bort, uppdatera och fråga åtgärder med hjälp av sina SDK: er. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Utveckla med Azure Cosmos DB tabell API

Just nu är den [Azure Cosmos DB tabell API](table-introduction.md) har fyra SDK: er för utveckling: 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK. Det här biblioteket har samma klasser och metoden signaturer som allmänheten [Windows Azure Storage SDK: N](https://www.nuget.org/packages/WindowsAzure.Storage), men har även möjlighet att ansluta till Azure Cosmos DB konton med tabell-API. 
- [Python SDK](table-sdk-python.md). Nya Azure Cosmos DB Python SDK är den enda SDK som stöder Azure Table storage i Python. Detta SDK ansluter med både Azure Table storage och Azure Cosmos DB tabell API.
- [Java SDK](table-sdk-java.md). Den här Azure Storage SDK: N har möjlighet att ansluta till Azure Cosmos DB konton med tabell-API.
- [Node.js SDK](table-sdk-nodejs.md). Den här Azure Storage SDK: N har möjlighet att ansluta till Azure Cosmos DB konton med tabell-API.

Ytterligare information om hur du arbetar med tabell-API finns i den [vanliga frågor och svar: utveckla med tabell-API](faq.md#develop-with-the-table-api) artikel.

## <a name="developing-with-the-azure-table-storage"></a>Utveckla med Azure Table storage

[Azure Table storage](table-storage-overview.md) har många SDK: er som är tillgängliga och självstudier tillgängliga, som nu är tillgängliga i den [Azure Table storage](table-storage-overview.md) avsnitt. Dessa artiklar uppdateras som samverkan mellan Azure Table storage SDK: er och Azure Cosmos DB tabell-API: er blir tillgänglig.  





