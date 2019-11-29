---
title: Så här konfigurerar du kvarhållning i din miljö – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du konfigurerar kvarhållning i din Azure Time Series Insightss miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 954e61aec5b622d44435a8a5b196fdbfed33c564
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561217"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurera kvarhållning i Time Series Insights

Den här artikeln beskriver hur du konfigurerar **tiden för datakvarhållning** och **lagrings gränsen överskreds** i Azure Time Series Insights.

## <a name="summary"></a>Sammanfattning

Varje Azure Time Series Insights miljö har en inställning för att konfigurera **data lagrings tid**. Värdet sträcker sig från 1 till 400 dagar. Data tas bort baserat på miljöns lagrings kapacitet eller varaktigheten för kvarhållning (1-400), beroende på vilket som kommer först.

Varje Time Series Insights miljö har en ytterligare **gräns för lagrings utrymme**som har överskridits. Den här inställningen styr ingångs-och rensnings beteende när den maximala kapaciteten för en miljö nås. Du kan välja mellan två beteenden:

- **Rensa gamla data** (standard)
- **Pausa ingress**

Detaljerad information för att bättre förstå de här inställningarna finns [i förstå kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Leta upp din befintliga Time Series Insights-miljö. Välj **alla resurser** på menyn på vänster sida av Azure Portal. Välj Time Series Insights-miljö.

1. Under rubriken **Inställningar** väljer du **lagrings konfiguration**.

    [![väljer Inställningar och sedan konfigurera](media/data-retention/1-configure-data-retention.png)](media/data-retention/1-configure-data-retention.png#lightbox)

1. Välj tids perioden för **data lagring (i dagar)** för att konfigurera kvarhållning med skjutreglaget eller Skriv ett tal i text rutan.

1. Observera **kapacitets** inställningen, eftersom den här konfigurationen påverkar den maximala mängden data händelser och den totala lagrings kapaciteten för att lagra data.

1. Växla inställningen för **lagrings gränsen överskreds** . Välj **Rensa gamla data** eller **pausa inträngande** beteende.

    [![data kvarhållning – Godkänn och spara.](media/data-retention/2-accept-and-save.png)](media/data-retention/2-accept-and-save.png#lightbox)

1. Läs dokumentationen för att förstå de potentiella riskerna med data förlust. Konfigurera ändringarna genom att välja **Spara** .

## <a name="next-steps"></a>Nästa steg

- Mer information finns [i förstå kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).

- Lär dig [hur du skalar din Time Series Insightss miljö](time-series-insights-how-to-scale-your-environment.md).

- Lär dig [att planera ut din miljö](time-series-insights-environment-planning.md).