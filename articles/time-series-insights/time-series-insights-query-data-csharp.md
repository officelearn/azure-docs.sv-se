---
title: Fråga efter data från en Azure Time Series Insights GA-miljö med C# kod | Microsoft Docs
description: Den här artikeln beskriver hur du frågar efter data från en Azure Time Series Insights-miljö genom att skriva en anpassad app som skrivits i C# (C-sharp) .NET-språk.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: 5e8b8d47b04d7d0b93bc699064ee414bf4429c4a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510204"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Fråga efter data från Azure Time Series Insights GA-miljö genomC#

Detta C# exemplet visar hur du frågar efter data från Azure Time Series Insights GA-miljö. 

Exemplet visar flera grundläggande exempel på användning av fråge-API:

1. Hämta åtkomsttoken via Azure Active Directory-API som en förberedelse steg. Vidarebefordra denna token i den `Authorization` huvudet i varje fråge-API-begäran. För att konfigurera icke-interaktiva program, se [autentisering och auktorisering](time-series-insights-authentication-and-authorization.md). Kontrollera också att alla konstanter som definierats i början av exemplet är korrekt inställda.
1. I listan över miljöer som användaren har åtkomst till hämtas. En av miljöerna har hämtats som den intressanta miljön och ytterligare data krävs för den här miljön.
1. Som ett exempel på en HTTPS-begäran begärs tillgänglighetsdata för den intressanta miljön.
1. Som ett exempel på en webbsocket-begäran begärs händelsens aggregerade data för den intressanta miljön. Data krävs för hela tillgänglighetstidsintervallet.

> [!NOTE]
> Exempelkoden finns på [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-ga-preview-sample).

## <a name="project-dependencies"></a>Projektberoenden

Lägg till NuGet-paket `Microsoft.IdentityModel.Clients.ActiveDirectory` och `Newtonsoft.Json`.

## <a name="c-example"></a>C#-exempel

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du frågar den [fråge-API-referens](/rest/api/time-series-insights/ga-query-api).

- Läs om hur du [ansluta ett enkelsidigt JavaScript](tutorial-create-tsi-sample-spa.md) till Time Series Insights.