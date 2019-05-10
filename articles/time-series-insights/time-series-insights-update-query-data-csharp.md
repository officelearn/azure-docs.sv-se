---
title: Fråga efter data från en förhandsversion av Azure Time Series Insights-miljö med C# kod | Microsoft Docs
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
ms.openlocfilehash: ebd5cb92b510da56446ca9e559b03a56cb2af7cf
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515286"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Fråga efter data från förhandsversionen av Azure Time Series Insights-miljö genomC#

Detta C# exemplet visar hur du frågar efter data från en förhandsversion av Azure Time Series Insights-miljö.

Exemplet visar flera grundläggande exempel på användning av fråge-API:

1. Hämta åtkomsttoken via Azure Active Directory-API som en förberedelse steg. Vidarebefordra denna token i den `Authorization` huvudet i varje fråge-API-begäran. För att konfigurera icke-interaktiva program, se [autentisering och auktorisering](time-series-insights-authentication-and-authorization.md). Kontrollera också att alla konstanter som definierats i början av exemplet är korrekt inställda.
1. I listan över miljöer som användaren har åtkomst till hämtas. En av miljöerna har hämtats som den intressanta miljön och ytterligare data krävs för den här miljön.
1. Som ett exempel på en HTTPS-begäran begärs tillgänglighetsdata för den intressanta miljön.
1. Som ett exempel på en webbsocket-begäran begärs händelsens aggregerade data för den intressanta miljön. Data krävs för hela tillgänglighetstidsintervallet.

> [!NOTE]
> Den här exempelkod är också tillgängliga på [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>C#-exempel

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Kodexemplet ovan kan köras utan att ändra standardvärdena för miljön.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du frågar den [fråge-API-referens](/rest/api/time-series-insights/preview-query).

- Läs om hur du [ansluta ett enkelsidigt JavaScript](tutorial-create-tsi-sample-spa.md) till Time Series Insights.