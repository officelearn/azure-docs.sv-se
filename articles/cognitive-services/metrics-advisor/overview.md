---
title: Vad är tjänsten Metrics Advisor?
titleSuffix: Azure Cognitive Services
description: Vad är Metrics Advisor?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 0de93eff0fc422bc28fe7b2c42c295c8d8b1acc4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344478"
---
# <a name="what-is-metrics-advisor-preview"></a>Vad är Metrics Advisor (för hands version)? 

Mått Advisor är en del av Azure Cognitive Services som använder AI för att utföra data övervakning och avvikelse identifiering i Time Series-data. Tjänsten automatiserar processen med att tillämpa modeller på dina data och tillhandahåller en uppsättning API: er webbaserade arbets yta för data inmatning, avvikelse identifiering och diagnostik – utan att behöva känna till Machine Learning. Använd Metrics Advisor för att:

* Analysera flerdimensionella data från flera data källor 
* Identifiera och korrelera avvikelser
* Konfigurera och finjustera den avvikelse identifierings modell som används på dina data
* Diagnostisera avvikelser och hjälp med rotor Saks analys. 

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Översikt över Metrics Advisor":::

## <a name="connect-to-a-variety-of-data-sources"></a>Anslut till en mängd olika data källor

Metrics Advisor kan ansluta till och [mata in flerdimensionella mått](how-tos/onboard-your-data.md) data från många data lager, inklusive: SQL Server, Azure Blob Storage, MongoDB och mycket annat. 

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Lättanvänd och anpassningsbar avvikelse identifiering

* Mått Advisor väljer automatiskt den bästa modellen för dina data utan att behöva känna till någon maskin inlärning. 
* Övervaka automatiskt varje tids serie i [flerdimensionella mått](glossary.md#multi-dimensional-metric).
* Använd [parameter justering](how-tos/configure-metrics.md) och [interaktiv feedback](how-tos/anomaly-feedback.md) för att anpassa modellen som används på dina data och framtida avvikelse identifierings resultat.


## <a name="real-time-alerts-through-multiple-channels"></a>Real tids aviseringar via flera kanaler

När avvikelser upptäcks kan du [Skicka real tids aviseringar](how-tos/alerts.md) via flera kanaler med hjälp av hookar, till exempel: e-posthooker, Webhooks och Azure DevOps-hookar. Med flexibla aviserings regler kan du anpassa vilka aviseringar som ska skickas och var.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Smart Diagnostic Insights genom att analysera avvikelser

Analysera avvikelser som har identifierats på flerdimensionella mått och generera [Smart Diagnostic Insights](how-tos/diagnose-incident.md) inklusive de flesta sannolika rotor saken, diagnostiska träd, mått borrning och mycket annat. Genom att konfigurera [mått diagram](how-tos/metrics-graph.md)kan kors mått analys aktive ras för att hjälpa dig att visualisera incidenter.


## <a name="typical-workflow"></a>Typiskt arbetsflöde

Arbets flödet är enkelt: när du har registrerat dina data kan du finjustera avvikelse identifieringen och skapa konfigurationer som passar ditt scenario.

1. [Skapa en Azure-resurs](../cognitive-services-apis-create-account.md) för Metrics Advisor. 
2. Bygg din första övervakare med hjälp av webb portalen.
    1. Registrera dina data
    2. Finjustera avvikelse identifiering
    3. Prenumerera på aviseringar
    4. Visa diagnostiska insikter
3. Använd REST API för att anpassa din instans.

## <a name="next-steps"></a>Nästa steg

* Utforska en snabb start: [övervaka ditt första mått på webben](quickstarts/web-portal.md).
* Utforska en snabb start: [Använd REST-API: er för att anpassa din lösning](./quickstarts/rest-api-and-client-library.md).