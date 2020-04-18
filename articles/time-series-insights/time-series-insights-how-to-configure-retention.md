---
title: Konfigurera kvarhållning i din miljö - Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du konfigurerar kvarhållning i azure time series insights-miljön.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 524e47e5be142b720687ad48ee9407da23284bd4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605056"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurera kvarhållning i Time Series Insights

I den här artikeln beskrivs hur du konfigurerar **datalagringstid** och **lagringsgränsen överskred beteendet** i Azure Time Series Insights.

## <a name="summary"></a>Sammanfattning

Varje Azure Time Series Insights-miljö har en inställning för att konfigurera **datalagringstid**. Värdet sträcker sig från 1 till 400 dagar. Data raderas baserat på miljöns lagringskapacitet eller lagringstiden (1-400), beroende på vilket som inträffar först.

Varje Time Series Insights-miljö har en ytterligare inställning **Lagringsgräns som överskridits.** Den här inställningen styr inträngnings- och rensningsbeteende när en miljös maximala kapacitet har uppnåtts. Det finns två beteenden att välja mellan:

- **Rensa gamla data** (standard)
- **Pausa inträngning**

Detaljerad information för att bättre förstå dessa inställningar finns [i Förstå lagring i Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Hitta din befintliga Time Series Insights-miljö. Välj **Alla resurser** på menyn till vänster i Azure-portalen. Välj Time Series Insights-miljö.

1. Under rubriken **Inställningar** väljer du **Lagringskonfiguration**.

    [![Under Inställningar väljer du Lagringskonfiguration](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Välj **datakvarhållningstiden (i dagar)** för att konfigurera kvarhållningen med hjälp av skjutreglaget eller skriv ett tal i textrutan.

1. Observera **inställningen Kapacitet,** eftersom den här konfigurationen påverkar den maximala mängden datahändelser och den totala lagringskapaciteten för lagring av data.

1. Växla **lagringsgränsen överskred beteendeinställningen.** Välj **Rensa gamla data** eller **Pausa inträngningsbeteende.**

    [![Pausa inträngning - acceptera och spara.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Granska dokumentationen för att förstå de potentiella riskerna med dataförlust. Välj **Spara** om du vill konfigurera ändringarna.

## <a name="next-steps"></a>Nästa steg

- Mer information finns [i Förstå kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).

- Lär dig hur du [skalar time series insights-miljön](time-series-insights-how-to-scale-your-environment.md).

- Läs mer om [att planera din miljö.](time-series-insights-environment-planning.md)
