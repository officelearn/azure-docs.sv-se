---
title: Fråga efter data från en för hands C# versions miljö med-Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du frågar efter data från en Azure Time Series Insights miljö med hjälp av en C#app som skrivits i.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: a1d32bf7ea296ed2c4ed9351fcefe400c03effa5
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861446"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Fråga efter data från Azure Time Series Insights Preview-miljön med hjälp avC#

Det C# här exemplet visar hur du frågar efter data från Azure Time Series Insights för hands versions miljön.

Exemplet visar flera grundläggande exempel på användning av fråge-API:

1. Som förberedelse steg hämtar du åtkomsttoken via Azure Active Directory-API: et. Skicka denna token i `Authorization`-rubriken för varje fråge-API-begäran. För att konfigurera icke-interaktiva program, Läs [autentisering och auktorisering](time-series-insights-authentication-and-authorization.md). Se också till att alla konstanter som definieras i början av exemplet är korrekt inställda.
1. Listan över miljöer som användaren har åtkomst till hämtas. En av miljöerna hämtas som en miljö av intresse och ytterligare data efter frågas i den här miljön.
1. Som ett exempel på en HTTPS-begäran begärs tillgänglighetsdata för den intressanta miljön.
1. Innehåller ett exempel på stöd för automatisk generering av SDK från [Azure AutoRest](https://github.com/Azure/AutoRest).

> [!NOTE]
> Exempel koden och hur du kompilerar och kör den finns på [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>C#exempel

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Kod exemplet ovan kan köras utan att ändra standard miljö värden.

## <a name="next-steps"></a>Nästa steg

- Läs mer om frågor i [API-referensen för frågor](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Läs hur du [ansluter en JavaScript-app med hjälp av klient-SDK](https://github.com/microsoft/tsiclient) för att Time Series Insights.
