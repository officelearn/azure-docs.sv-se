---
title: Så här konfigurerar du kvarhållning i Azure Time Series Insightss miljön | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar kvarhållning i Azure Time Series Insightss miljön.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 9986f57b05032c1e12769d59781e8b7aca443abb
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298976"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurera kvarhållning i Time Series Insights

Den här artikeln beskriver hur du konfigurerar **tiden för datakvarhållning** och **lagrings gränsen överskreds** i Azure Time Series Insights.

## <a name="summary"></a>Sammanfattning

Varje Azure Time Series Insights miljö har en inställning för att konfigurera **data lagrings tid**. Värdet sträcker sig från 1 till 400 dagar. Data tas bort baserat på miljöns lagrings kapacitet eller varaktigheten för kvarhållning (1-400), beroende på vilket som kommer först.

Varje TSD-miljö har ytterligare ett **gräns värde för lagrings utrymme**som har överskridits. Den här inställningen styr ingångs-och rensnings beteende när den maximala kapaciteten för en miljö nås. Du kan välja mellan två beteenden:

- **Rensa gamla data** (standard)
- **Pausa ingress**

Detaljerad information för att bättre förstå de här inställningarna finns [i förstå kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Leta upp din befintliga Time Series Insights-miljö. Välj **alla resurser** på menyn på vänster sida av Azure Portal. Välj Time Series Insights-miljö.

1. Under rubriken **Inställningar** väljer du **Konfigurera**.

    [![Select inställningar och konfigurera sedan](media/data-retention/1-configure-data-retention.png)](media/data-retention/1-configure-data-retention.png#lightbox)

1. Välj tids perioden för **data lagring (i dagar)** för att konfigurera kvarhållning med skjutreglaget eller Skriv ett tal i text rutan.

1. Observera **kapacitets** inställningen, eftersom den här konfigurationen påverkar den maximala mängden data händelser och den totala lagrings kapaciteten för att lagra data.

1. Växla inställningen för **lagrings gränsen överskreds** . Välj **Rensa gamla data** eller **pausa inträngande** beteende.

    [![Data kvarhållning Godkänn och spara.](media/data-retention/2-accept-and-save.png)](media/data-retention/2-accept-and-save.png#lightbox)

1. Acceptera kryss rutan som anger att du har granskat dokumentationen och förstår de potentiella riskerna med data förlust. Konfigurera ändringarna genom att välja **Spara** .

## <a name="next-steps"></a>Nästa steg

- Mer information finns [i förstå kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).