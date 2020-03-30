---
title: Frågedata från en GA-miljö med C#-kod - Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du frågar data från en Azure Time Series Insights-miljö med hjälp av en anpassad app som är skriven i C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 9f7819974e3548baf5e10f0bf9a2d656d9412beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987979"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Fråga data från Azure Time Series Insights GA-miljön med C #

Det här C#-exemplet visar hur du använder [GA Query API:er](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) för att fråga data från Azure Time Series Insights GA-miljöer.

> [!TIP]
> Visa GA C#-kodexempel på [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="summary"></a>Sammanfattning

Exempelkoden nedan visar följande funktioner:

* Hämta en åtkomsttoken via Azure Active Directory med [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Så här skickar du den `Authorization` förvärvade åtkomsttoken i huvudet på efterföljande Query API-begäranden. 

* Exemplet anropar var och en av API:erna för 2-fråga som visar hur HTTP-begäranden görs till:
    * [Hämta Miljö-API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) för att returnera de miljöer som användaren har tillgång till
    * [Api för miljötillgänglighet](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Hämta API för miljömetadata](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) för att hämta miljömetadata
    * [Api för att få miljöer händelser](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Api för hämta miljöaggregat](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Så här interagerar du med API:erna för GA-frågor med WSS för att skicka meddelandet:

   * [Hämta api för miljöhändelser strömmade](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Hämta Flöden av miljöaggregat](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Förutsättningar och inställningar

Gör följande innan du kompilerar och kör exempelkoden:

1. [Etablera en GA Azure Time Series Insights-miljö.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)
1. Konfigurera din Azure Time Series Insights-miljö för Azure Active Directory enligt beskrivningen i [Autentisering och auktorisering](time-series-insights-authentication-and-authorization.md). 
1. Installera de nödvändiga projektberoendena.
1. Redigera exempelkoden nedan genom att ersätta varje **#DUMMY#** med lämplig miljöidentifierare.
1. Kör koden i Visual Studio.

## <a name="project-dependencies"></a>Projektberoenden

Vi rekommenderar att du använder den senaste versionen av Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Version 16.4.2+

Exempelkoden har två obligatoriska beroenden:

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - 3.13.9-paketet.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 9.0.1 paket.

Ladda ned paketen i Visual Studio 2019 genom att välja alternativet > **Byggversionslösning.** **Build**

Alternativt kan du lägga till paketen med [NuGet 2.12+](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C#exempelkod

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Nästa steg

- Mer information om frågor finns i [referensen för fråge-API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Läs om hur du [ansluter en JavaScript-app med klienten SDK](https://github.com/microsoft/tsiclient) till Time Series Insights.
