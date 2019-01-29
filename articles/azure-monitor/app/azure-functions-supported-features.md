---
title: Funktioner som stöds av Azure Application Insights – Azure Functions | Microsoft Docs
description: Application Insights funktioner som stöds för Azure Functions
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 06feece050835b2b9188eb702210770b44a6b49c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185826"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Funktioner som stöds av Application Insights för Azure Functions

Azure Functions erbjuder [inbyggd integrering](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) med Application Insights, som är tillgängligt via gränssnittet ILogger. Nedan visas en lista över funktioner som för närvarande stöds. Granska Azure Functions-guide för [komma igång](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Funktioner som stöds

| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **Automatisk insamling av**        |                 |                   |               
| &bull; Begäranden                     | Ja             | Ja               | 
| &bull; Undantag                   | Ja             | Ja               | 
| &bull; Beroenden                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Ja               | 
| | | | 
| **Funktioner som stöds**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Ja             | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; Säker kontrollkanal|                 | Ja               | 
| &bull; Sampling                     | Ja             | Ja               | 
| &bull; Pulsslag                   |                 | Ja               | 
| | | | 
| **Korrelation**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Ja               | 
| &bull; EventHub                       |                   | Ja               | 
| | | | 
| **Konfigurerbara**                      |                   |                   |           
| &bull;Konfigureras fullt ut.<br/>Se [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) anvisningar.<br/>Se [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) för alla alternativ.               |                   | Ja                   | 


## <a name="live-metrics--secure-control-channel"></a>Live mått och säker kontrollkanal

De Anpassa filter kriterier som du anger skickas tillbaka till komponenten Live Metrics i Application Insights SDK. Filtren kan innehålla känslig information, till exempel customerIDs. Du kan göra kanalen säker med en hemlig API-nyckel. Se [skydda kontrollkanalen](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) anvisningar.

## <a name="sampling"></a>Samling

Azure Functions kan Sampling som standard i konfigurationen. Mer information finns i [konfigurera Sampling](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).
