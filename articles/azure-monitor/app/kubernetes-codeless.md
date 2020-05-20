---
title: Övervaka program på Azure Kubernetes service (AKS) med Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor integreras sömlöst med ditt program som körs på Kubernetes och gör att du kan övervaka dina problem med dina appar på en gång.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: aaca07febedc609fb0dc9b9ca0e536510f9ca8d1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704522"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Övervakning av program vara med noll Kubernetes-Azure Monitor Application Insights

> [!IMPORTANT]
>  För närvarande kan du aktivera övervakning av Java-appar som körs på Kubernetes utan att instrumentera din kod – Använd den [fristående Java-agenten](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Även om lösningen för att sömlöst aktivera program övervakning finns i Works för andra språk, använder du SDK: er för att övervaka dina appar som körs på AKS: [ASP.net Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node. js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [Java Script](https://docs.microsoft.com/azure/azure-monitor/app/javascript)och [python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Program övervakning utan att behöva instrumentera koden
För närvarande kan du aktivera program övervakning utan att behöva instrumentera koden med Java. Använd SDK: er för att övervaka program på andra språk. 

## <a name="java"></a>Java
När den är aktive rad samlar Java-agenten automatiskt in en mängd begär Anden, beroenden, loggar och mått från de mest använda biblioteken och ramverken.

Följ [de detaljerade anvisningarna](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) för att övervaka Java-appar som körs i Kubernetes-appar, samt andra miljöer. 

## <a name="other-languages"></a>Andra språk

För program på andra språk rekommenderar vi för närvarande användning av SDK: er:
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) och [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* Få en översikt över [distribuerad spårning](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) och se vilken [program karta](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) som kan utföras för ditt företag