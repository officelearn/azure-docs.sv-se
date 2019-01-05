---
title: Azure Cosmos DB Table API .NET Standard SDK och resurser
description: Lär dig allt om Azure Cosmos DB Table API och .NET Standard SDK inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/18/2018
ms.openlocfilehash: 5e0282c766527f8210e36f6e916518a252200041
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038899"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB-tabell .NET Standard-API: Ladda ned och viktig information
> [!div class="op_single_selector"]

> * [NET](table-sdk-dotnet.md)
> * [.NET-standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Hämta SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Aktuella framework som stöds**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Viktig information

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-Preview
* Lägg till stöd för grundläggande CRUD, batch och frågor åtgärder mot Azure Storage-tabell slutpunkter. [!NOTE] Vissa funktioner i föregående tabell SDK för Azure Storage stöds inte ännu, till exempel kryptering.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-Preview
* Azure Cosmos DB tabell SDK för .NET Standard är ett plattformsoberoende .NET-bibliotek som tillhandahåller effektiv åtkomst till datamodellen tabell Cosmos DB. Den här första versionen stöder den fullständiga uppsättningen tabell och entiteten CRUD + fråga funktioner med liknande API: er som den [Cosmos DB tabell SDK för .NET Framework](table-sdk-dotnet.md). [!NOTE] Azure lagringstabell slutpunkter stöds inte ännu i 0.9.1-preview-versionen.

## <a name="release-and-retirement-dates"></a>Versionen och dras tillbaka datum
Microsoft meddelar minst **12 månader** förväg dra tillbaka en SDK för att utjämna övergången till en nyare/stöds version.

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [0.10.0-Preview](#0.10.0-preview) |18 december 2018 |--- |
| [0.9.1-Preview](#0.9.1-preview) |Den 18 oktober 2018 |--- |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Läs mer om Azure Cosmos DB Table API i [introduktion till Azure Cosmos DB Table API](table-introduction.md). 
