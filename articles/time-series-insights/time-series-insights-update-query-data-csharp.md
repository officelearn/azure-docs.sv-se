---
title: Fråga efter data från en Azure Time Series Insights för hands C# versions miljö med kod | Microsoft Docs
description: Den här artikeln beskriver hur du frågar data från en Azure Time Series Insights C# -miljö genom att koda en anpassad app som skrivits i (C-Sharp) .net-språket.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 6d2395dcfae5d512412048a683a7f1397967499e
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978655"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Fråga efter data från Azure Time Series Insights Preview-miljön med hjälp avC#

Det C# här exemplet visar hur du frågar efter data från Azure Time Series Insights för hands versions miljön.

Exemplet visar flera grundläggande exempel på användning av fråge-API:

1. Som förberedelse steg hämtar du åtkomsttoken via Azure Active Directory-API: et. Skicka denna token i `Authorization`-huvudet för varje fråge-API-begäran. Information om hur du konfigurerar icke-interaktiva program finns i [autentisering och auktorisering](time-series-insights-authentication-and-authorization.md). Se också till att alla konstanter som definieras i början av exemplet är korrekt inställda.
1. Listan över miljöer som användaren har åtkomst till hämtas. En av miljöerna hämtas som en miljö av intresse och ytterligare data efter frågas i den här miljön.
1. Som ett exempel på en HTTPS-begäran begärs tillgänglighetsdata för den intressanta miljön.
1. Som ett exempel på en webbsocket-begäran begärs händelsens aggregerade data för den intressanta miljön. Data krävs för hela tillgänglighetstidsintervallet.

> [!NOTE]
> Den här exempel koden finns också i [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>C#exempel

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Kod exemplet ovan kan köras utan att ändra standard miljö värden.

## <a name="next-steps"></a>Nästa steg

- Läs mer om frågor i [API-referensen för frågor](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Läs hur du [ansluter en JavaScript-app med hjälp av klient-SDK](https://github.com/microsoft/tsiclient) för att Time Series Insights.