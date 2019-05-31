---
title: Konfigurera kvarhållning i Azure Time Series Insights-miljön | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar kvarhållning i din Azure Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: f5f34983d1818679450249aa40bbb325e743cc57
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239010"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurera kvarhållning i Time Series Insights

Den här artikeln beskriver hur du konfigurerar **datalagringstid** och **lagringsgräns överskred beteende** i Azure Time Series Insights.

## <a name="summary"></a>Sammanfattning

Alla miljöer för Time Series Insights (TSI) har en inställning för att konfigurera **datalagringstid**. Värdet sträcker sig från 1 till 400 dagar. Data tas bort baserat på miljön storage kvarhållning eller kapacitet (1 400), beroende på vilket som inträffar först.

Alla TSI-miljöer har en ytterligare inställning **lagringsgräns överskred beteende**. Den här inställningen styr beteendet för ingående och rensa när maxkapacitet för en miljö har uppnåtts. Det finns två beteenden att välja mellan:

- **Radera gamla data** (standard)
- **Pausa ingress**

Detaljerad information att bättre förstå de här inställningarna finns [Understanding kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Leta upp din befintliga Time Series Insights-miljö. Välj **alla resurser** på menyn på vänster sida av Azure-portalen. Välj Time Series Insights-miljö.

1. Under den **inställningar** väljer **konfigurera**.

1. Välj den **datalagringstid** att konfigurera den kvarhållning av säkerhetskopior med hjälp av skjutreglaget eller ange ett tal i textrutan.

1. Obs den **kapacitet** anger, eftersom denna konfiguration påverkar den maximala mängden Datahändelser och total lagringskapacitet för att lagra data.

1. Visa/Dölj de **lagringsgräns överskred beteende** inställningen. Välj **Radera gamla data** eller **pausa ingående** beteende.

1. Välj **spara** konfigurera ändringarna.

## <a name="next-steps"></a>Nästa steg

- Mer information finns [Understanding kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).