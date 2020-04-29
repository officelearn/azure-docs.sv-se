---
title: Fråga efter data från en för hands versions miljö med C#-Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du frågar efter data från en Azure Time Series Insights miljö med hjälp av en app som skrivits i C#.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81379820"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Fråga efter data från Azure Time Series Insights Preview-miljö med C #

Det här C#-exemplet visar hur du frågar efter data från [API: erna för för hands versions data åtkomst](https://docs.microsoft.com/rest/api/time-series-insights/preview) i Azure Time Series Insights Preview-miljöer.

> [!TIP]
> Visa för hands version av C# [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)-kod exempel på.

## <a name="summary"></a>Sammanfattning

Exempel koden nedan visar följande funktioner:

* Stöd för automatisk generering av SDK från [Azure AutoRest](https://github.com/Azure/AutoRest).
* Så här hämtar du en åtkomsttoken via Azure Active Directory med [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Så här skickar du den hämtade åtkomsttoken i `Authorization` rubriken för efterföljande API-begäranden för data åtkomst. 
* Exemplet innehåller ett konsol gränssnitt som demonstrerar hur HTTP-begäranden görs till:

    * [API för för hands versions miljöer](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Hämta tillgänglighets-API för miljöer](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) och [Hämta API för Event schema](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Förhandsgranska fråge-API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Hämta händelse-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), [Hämta serie-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)och [Hämta API för sammanställd serie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [API: er för Time Series-modellen](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Hämta hierarkier API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) och [hierarkier batch-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Hämta API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) och [typer batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Hämta instanser API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) och [instanser batch-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Avancerade [Sök](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) -och [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) -funktioner.

## <a name="prerequisites-and-setup"></a>Krav och installation

Slutför följande steg innan du kompilerar och kör exempel koden:

1. [Etablera en förhands granskning Azure Time Series Insightss](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) miljö.
1. Konfigurera din Azure Time Series Insightss miljö för Azure Active Directory enligt beskrivningen i [autentisering och auktorisering](time-series-insights-authentication-and-authorization.md). 
1. Kör [GenerateCode. bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) enligt vad som anges i [Readme.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) för att generera Time Series Insights för hands versions klient beroenden.
1. Öppna `TSIPreviewDataPlaneclient.sln` lösningen och ange `DataPlaneClientSampleApp` som standard projekt i Visual Studio.
1. Installera de nödvändiga projekt beroendena med stegen som beskrivs [nedan](#project-dependencies) och kompilera exemplet till en körbar `.exe` fil.
1. Kör `.exe` filen genom att dubbelklicka på den.

## <a name="project-dependencies"></a>Projektberoenden

Vi rekommenderar att du använder den senaste versionen av Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -version 16.4.2 +

Exempel koden har flera nödvändiga beroenden som kan visas i filen [packages. config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) .

Ladda ned paketen i Visual Studio 2019 genom att välja alternativet **build** > **build-lösning** . 

Du kan också lägga till varje paket med [NuGet 2.12 +](https://www.nuget.org/). Ett exempel:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Exempel kod för C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * Kod exemplet kan köras utan att ändra standard miljövariablerna.
> * Kod exemplet kompileras till en körbar app för .NET-konsolen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om frågor i [API-referensen för frågor](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Läs hur du [ansluter en JavaScript-app med hjälp av klient-SDK](https://github.com/microsoft/tsiclient) för att Time Series Insights.
