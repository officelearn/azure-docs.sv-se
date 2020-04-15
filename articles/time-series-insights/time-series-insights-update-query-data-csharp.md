---
title: Frågedata från en förhandsgranskningsmiljö med C# - Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du frågar data från en Azure Time Series Insights-miljö med hjälp av en app som är skriven i C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fbc2cbc29cb23a21e7d3713091fc22f01bb1b15a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379820"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Frågedata från förhandsversionen av Azure Time Series Insights med C #

Det här C#-exemplet visar hur du frågar data från [förhandsversionen av API:er för dataåtkomst](https://docs.microsoft.com/rest/api/time-series-insights/preview) i förhandsversionsmiljöer för azure time series insights.

> [!TIP]
> Visa exempel på förhandsgranskning av C#-kod på [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="summary"></a>Sammanfattning

Exempelkoden nedan visar följande funktioner:

* Stöd för automatisk SDK-generering från [Azure AutoRest](https://github.com/Azure/AutoRest).
* Hämta en åtkomsttoken via Azure Active Directory med [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Så här skickar du den `Authorization` förvärvade åtkomsttoken i huvudet på efterföljande API-begäranden för dataåtkomst. 
* Exemplet innehåller ett konsolgränssnitt som visar hur HTTP-begäranden görs till:

    * [API för förhandsgranskningsmiljöer](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Api för miljötillgänglighet](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) och [API för händelseschema](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Api för förhandsgranskning av fråga](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Hämta API för händelser,](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents) [hämta serie-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)och [hämta API i mängdserier](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [Api:er för tidsseriemodell](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Hämta API för hierarkier](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) och [batch-API för hierarkier](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Hämta API för typer](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) och [typer batch-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Hämta API-api för instanser](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) och [instanser batch-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Avancerade [sök-](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) och [TSX-funktioner.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="prerequisites-and-setup"></a>Förutsättningar och inställningar

Gör följande innan du kompilerar och kör exempelkoden:

1. [Etablera en förhandsgranskning av Azure Time Series Insights-miljö.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)
1. Konfigurera din Azure Time Series Insights-miljö för Azure Active Directory enligt beskrivningen i [Autentisering och auktorisering](time-series-insights-authentication-and-authorization.md). 
1. Kör [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) som anges i [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) för att generera klientberoendena för time series insights..
1. Öppna `TSIPreviewDataPlaneclient.sln` lösningen och `DataPlaneClientSampleApp` ange som standardprojekt i Visual Studio.
1. Installera de nödvändiga projektberoendena [below](#project-dependencies) med hjälp av stegen som `.exe` beskrivs nedan och kompilera exemplet till en körbar fil.
1. Kör `.exe` filen genom att dubbelklicka på den.

## <a name="project-dependencies"></a>Projektberoenden

Vi rekommenderar att du använder den senaste versionen av Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Version 16.4.2+

Exempelkoden har flera obligatoriska beroenden som kan visas i [filen packages.config.](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)

Ladda ned paketen i Visual Studio 2019 genom att välja alternativet > **Byggversionslösning.** **Build** 

Alternativt kan du lägga till varje paket med [NuGet 2.12+](https://www.nuget.org/). Ett exempel:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C#exempelkod

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * Kodexemplet kan köras utan att ändra standardmiljövariablerna.
> * Kodexemplet kompileras till en .NET-körbar konsolapp.

## <a name="next-steps"></a>Nästa steg

- Mer information om frågor finns i [referensen för fråge-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Läs om hur du [ansluter en JavaScript-app med klienten SDK](https://github.com/microsoft/tsiclient) till Time Series Insights.
