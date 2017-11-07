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
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Utveckla med Azure Cosmos DB tabell API och Azure Table storage

Azure Cosmos DB tabell API och Azure Table storage delar samma tabell datamodellen och exponera samma skapa, ta bort, uppdatera och fråga åtgärder med hjälp av sina SDK: er. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Utveckla med Azure Cosmos DB tabell API

Just nu är den [Azure Cosmos DB tabell API](table-introduction.md) har en .NET SDK som är tillgängliga, den [Windows Azure Storage Premium Table (förhandsgranskning)](https://aka.ms/premiumtablenuget). Det här biblioteket har samma klasser och metoden signaturer som allmänheten [Windows Azure Storage SDK: N](https://www.nuget.org/packages/WindowsAzure.Storage), men har även möjlighet att ansluta till Azure Cosmos DB konton med tabell-API (förhandsversion). En Snabbstart och självstudier med Azure Cosmos DB tabell API finns i följande artiklar:
- Snabbstart: [Azure Cosmos-DB: skapa ett .NET-program med tabell-API](create-table-dotnet.md)
- Självstudier: [Azure Cosmos DB: utveckla med tabell-API i .NET](tutorial-develop-table-dotnet.md)

Ytterligare information om hur du arbetar med tabell-API finns i den [vanliga frågor och svar: utveckla med tabell-API](faq.md#develop-with-the-table-api-preview) artikel.

## <a name="developing-with-the-azure-table-storage"></a>Utveckla med Azure Table storage

[Azure Table storage](table-storage-overview.md) har många SDK: er som är tillgängliga och självstudier tillgängliga, som nu är tillgängliga i den [Azure Table storage](table-storage-overview.md) avsnitt. Dessa artiklar uppdateras som samverkan mellan Azure Table storage SDK: er och Azure Cosmos DB tabell-API: er blir tillgänglig.  





