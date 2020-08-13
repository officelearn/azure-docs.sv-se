---
title: Fråga efter data från en gen1-miljö med C# Code-Azure Time Series Insights gen1 | Microsoft Docs
description: Lär dig hur du frågar data från en Azure Time Series Insights gen1-miljö med hjälp av en anpassad app som skrivits i C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: a7307a5ecdc5f6aa4b90480fa769f87317605a61
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168073"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>Fråga efter data från Azure Time Series Insights gen1-miljön med hjälp av C Sharp

Det här C#-exemplet visar hur du använder [gen1-fråge-API: er](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query) för att fråga efter data från Azure Time Series Insights gen1-miljöer.

> [!TIP]
> Visa gen1 C#-kod exempel på [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample) .

## <a name="summary"></a>Sammanfattning

Exempel koden nedan visar följande funktioner:

* Så här hämtar du en åtkomsttoken via Azure Active Directory med [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Så här skickar du den hämtade åtkomsttoken i `Authorization` rubriken för efterföljande frågor om API-begäranden.

* Exemplet anropar var och en av gen1-fråge-API: erna som demonstrerar hur HTTP-begäranden görs till:
  * [Hämta miljöer API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environments-api) för att returnera de miljöer som användaren har åtkomst till
  * [Hämta miljö tillgänglighets-API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * [Hämta API för miljö-metadata](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api) för att hämta miljömetadata
  * [Hämta händelse-API för miljöer](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [Hämta API för miljö samling](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* Så här interagerar du med gen1 fråga-API: er med hjälp av WSS för att skicka följande:

  * [Hämta direktuppspelade API: er för miljö händelser](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [Hämta direktuppspelade API för miljö Aggregator](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Krav och installation

Slutför följande steg innan du kompilerar och kör exempel koden:

1. [Etablera en Gen1 Azure Time Series Insightss](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) miljö.
1. Konfigurera din Azure Time Series Insightss miljö för Azure Active Directory enligt beskrivningen i [autentisering och auktorisering](time-series-insights-authentication-and-authorization.md).
1. Installera de projekt beroenden som krävs.
1. Redigera exempel koden nedan genom att ersätta varje **#DUMMY #** med rätt miljö identifierare.
1. Kör koden i Visual Studio.

## <a name="project-dependencies"></a>Projektberoenden

Vi rekommenderar att du använder den senaste versionen av Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -version 16.4.2 +

Exempel koden har två nödvändiga beroenden:

* [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9-paket.
* [Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json) 9.0.1-paket.

Ladda ned paketen i Visual Studio 2019 genom att välja alternativet **build**  >  **build-lösning** .

Du kan också lägga till paketen med [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Exempel kod för C#

Time Series Insights gen1-exempel hittar du på [csharpquery-exempel](https://github.com/Azure-Samples/Azure-Time-Series-Insights#tsi-gen1)

## <a name="next-steps"></a>Nästa steg

* Läs mer om frågor i [API-referensen för frågor](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api).

* Läs hur du [ansluter en JavaScript-app med hjälp av klient-SDK](https://github.com/microsoft/tsiclient) för att Time Series Insights.
Azure-samples/Azure-Time-Series-Insights/gen1-Sample/csharp-TSD-gen1-Sample/program. CS
