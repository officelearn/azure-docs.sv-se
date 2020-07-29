---
title: Felsök Azure Cosmos DBs tjänsten otillgängligt undantag
description: Så här diagnostiserar och åtgärdar du Cosmos DB tjänst ej tillgängligt undantag
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294520"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnostisera och Felsök Azure Cosmos DB tjänsten är inte tillgänglig
SDK kunde inte ansluta till Azure Cosmos DB tjänsten.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för otillgängliga tjänst undantag.

### <a name="1-the-required-ports-are-not-enabled"></a>1. de portar som krävs är inte aktiverade.
Kontrol lera att alla [nödvändiga portar](performance-tips-dotnet-sdk-v3-sql.md#networking) är aktiverade.

## <a name="if-an-existing-application-or-service-started-getting-503"></a>Om ett befintligt program eller en tjänst har börjat få 503

### <a name="1-there-is-an-outage"></a>1. det har uppstått ett avbrott
Kontrol lera [Azure-statusen](https://status.azure.com/status) för att se om det finns ett pågående problem.

### <a name="2-snat-port-exhaustion"></a>2. SNAT-port överbelastning
Följ [port överbelastnings guide för SNAT](troubleshoot-dot-net-sdk.md#snat).

### <a name="3-the-required-ports-are-being-blocked"></a>3. de portar som krävs blockeras
Kontrol lera att alla [nödvändiga portar](performance-tips-dotnet-sdk-v3-sql.md#networking) är aktiverade.

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md)