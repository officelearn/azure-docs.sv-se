---
title: Översikt över Azure-behållare övervakning | Microsoft Docs
description: Den här artikeln innehåller en översikt över de olika metoderna som är tillgängliga i Azure för att övervaka behållare i Azure för att snabbt förstå ett kluster hälsotillstånd och tillgänglighet.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2018
ms.author: magoedte
ms.openlocfilehash: 0d511c1f6dfd482e5754741da15b2852ee77c11e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Översikt över övervakning behållare i Azure
Med Azure, kan du effektivt övervaka och hantera din arbetsbelastning distribueras på Azure-behållare med Kubernetes eller Docker. Det är viktigt att förstå hur behållare med flera mikrotjänster program utför för att leverera en tillförlitlig tjänst i större skala och övervakning planen. Den här artikeln innehåller en kort översikt över hanteringen och övervakningsfunktionerna i Azure för att du förstår varje och som är lämplig baserat på dina krav.

Med hjälp av [Azure Övervakare behållaren hälsa](monitoring-container-health.md), du kan visa prestanda och hälsotillståndet för din infrastruktur för Linux-behållaren och undersöka problem snabbt. Telemetrin lagras i logganalys-arbetsytan och integrerat i Azure-portalen där du kan utforska, filtrera och segment aggregerade data med instrumentpaneler för att hålla koll på belastning, prestanda och hälsa.  

För behållare som körs utanför Azure Kubernetes värdtjänsten, Log Analytics [lösning för Windows och Dockerbehållare](../log-analytics/log-analytics-containers.md) kan du visa och hantera dina Windows- och Docker behållare värdar. Från logganalys-arbetsytan kan du visa inventeringsinformation, prestanda och händelser från noder och behållare i miljön. Du kan visa detaljerad granskningsinformation visar kommandon som används med behållare och du kan felsöka behållare genom att visa och söka centraliserad loggar utan att fjärransluta till Docker- eller Windows-värdar.

För att uppnå heltäckande eller slutpunkt till slutpunkt övervakning av programmet, bör eventuella beroenden om det är en Azure- eller lokal resurs, övervakas med Azure-Monitor eller logganalys.  Applikationsnivån ska tas med för att lägga till ett extra lager av hälsotillstånd medvetenhet, både på plattformen och program med Application Insights. På nivån plattformen är Application Insights SDK för [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), och [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). För mikrotjänster program, det finns stöd för [Java](../application-insights/app-insights-java-get-started.md), [Node.js](../application-insights/app-insights-nodejs-quick-start.md), [.Net](../application-insights/app-insights-asp-net.md), [.Net Core](../application-insights/app-insights-asp-net-core.md), samt ett antal andra [språk/ramverk](../application-insights/app-insights-platforms.md). 

Annars problem hamnar Oidentifierad som kan påverka tillgängligheten för programmet och servicenivåavtal inte uppfylls.  
