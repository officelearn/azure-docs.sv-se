---
title: Fråga efter data från en Azure Time Series Insights GA- C# miljö med hjälp av kod | Microsoft Docs
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
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 66c79bed59c8966156d6f000e74e5300edc0245a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883924"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Fråga efter data från Azure Time Series Insights GA-miljön med hjälp avC#

Det C# här exemplet visar hur du frågar efter data från Azure Time Series Insights ga-miljön.

Exemplet visar flera grundläggande exempel på användning av fråge-API:

1. Som förberedelse steg hämtar du åtkomsttoken via Azure Active Directory-API: et. Skicka denna token i `Authorization` rubriken för varje fråge-API-begäran. Information om hur du konfigurerar icke-interaktiva program finns i [autentisering och auktorisering](time-series-insights-authentication-and-authorization.md). Se också till att alla konstanter som definieras i början av exemplet är korrekt inställda.
1. Listan över miljöer som användaren har åtkomst till hämtas. En av miljöerna hämtas som en miljö av intresse och ytterligare data efter frågas i den här miljön.
1. Som ett exempel på en HTTPS-begäran begärs tillgänglighetsdata för den intressanta miljön.
1. Som ett exempel på en webbsocket-begäran begärs händelsens aggregerade data för den intressanta miljön. Data krävs för hela tillgänglighetstidsintervallet.

> [!NOTE]
> Exempel koden finns på [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Projekt beroenden

Lägg till NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` - `Newtonsoft.Json`paket och.

## <a name="c-example"></a>C#exempel

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Nästa steg

- Läs mer om frågor i API-referensen för [frågor](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Läs hur du [ansluter en JavaScript-app med ett enda sidfel](tutorial-create-tsi-sample-spa.md) till Time Series Insights.