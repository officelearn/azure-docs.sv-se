---
title: "Azure Cosmos DB: .NET ändra Feed Processor API, SDK & resurser | Microsoft Docs"
description: "Läs mer om ändringen Feed Processor-API och SDK inklusive frisläppningsdatum, tillbakadragning datum och ändringar mellan varje version av .NET-ändra Feed Processor-SDK."
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/05/2017
ms.author: maquaran
ms.openlocfilehash: 962ad3aca82e34fa22da925a274a1e82e2086e03
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET ändra Feed Processor SDK: Hämta och viktig information
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET ändra Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Asynkrona Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

|   |   |
|---|---|
|**SDK-hämtningen**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-dokumentationen**|[Ändra Feed Processor biblioteket API-referensdokumentation](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Kom igång**|[Kom igång med ändringen Feed Processor .NET SDK](change-feed.md)|
|**Aktuella framework som stöds**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Viktig information

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Lägger till stöd för .NET Standard 2.0. Paketet stöder nu `netstandard2.0` och `net451` framework monikrar.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.17.0 och högre.
* Kompatibel med [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) versioner 1.5.1 och högre.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Åtgärdar problemet med beräkningen av uppskattning av återstående när ändringen Feed var tom eller inget arbete pågick.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.13.2 och högre.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Lägga till en metod för att få en uppskattning av återstående arbete som ska bearbetas i ändra Feed.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.13.2 och högre.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.14.1 och nedan.

## <a name="release--retirement-dates"></a>Versionen & pensionering datum
Microsoft meddelar notification minst **12 månader** innan du tar bort en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar bara lägga till den aktuella SDK, som vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt. 

Alla förfrågningar till Cosmos-databasen med en pensionerad SDK avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Datumet för tillbakadragandet |
| --- | --- | --- |
| [1.2.0](#1.2.0) |31 oktober 2017 |--- |
| [1.1.1](#1.1.1) |Den 29 augusti 2017 |--- |
| [1.1.0](#1.1.0) |13 augusti 2017 |--- |
| [1.0.0](#1.0.0) |07 juli 2017 |--- |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Läs mer om Cosmos-DB i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) sida. 

