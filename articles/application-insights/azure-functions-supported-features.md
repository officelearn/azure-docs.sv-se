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
ms.devlang: multiple
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 05958f35f80a53da27e020d367799519ef5a9bd7
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901591"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Funktioner som stöds av Application Insights för Azure Functions

Nedan visas listan över funktioner för för närvarande stöds i [Application Insights-integrering med Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring). Granska Azure Functions-guide för [komma igång](https://github.com/Azure/Azure-Functions/wiki/App-Insights).


| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights SDK för .NET**         | **2.5.0**             | **2.7.2**                 |
| | | | 
| **Automatisk insamling av**              |                   |                   |               
| &bull; Begäranden                           | Ja               | Ja               | 
| &bull; Undantag                         | Ja               | Ja               | 
| &bull; Beroenden               |                   |                   |               
| &mdash; HTTP                              |                   | Ja               | 
| &mdash; serviceBus                        |                   | Ja               | 
| &mdash; eventHub                          |                   | Ja               | 
| &mdash; SQL                               |                   | Ja               | 
| | | | 
| **Funktioner som stöds**                    |                   |                   |               
| &bull; QuickPulse/LiveMetrics                         | Ja               | Ja               | 
| &bull; Sampling                           | Ja               | Ja               | 
| &bull; Pulsslag                         |       | Ja               | 
| | | | 
| **Korrelation**                           |                   |                   |               
| &bull; serviceBus                         |                   | Ja               | 
| &bull; eventHub                           |                   | Ja               | 
| | | | 
| **Konfigurerbara**                  |                   |                   |           
| &bull;Konfigureras fullt ut.<br/>Se [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) anvisningar.<br/>Se [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) för alla alternativ.               |                   | Ja                   | 
