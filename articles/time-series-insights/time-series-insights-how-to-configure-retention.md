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
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: e5cc1489af1dce3a9a57b96a3240c63e0395c33a
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947234"
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurera kvarhållning i Time Series Insights

Den här artikeln beskriver hur du konfigurerar **tiden** för datakvarhållning och **lagrings gränsen överskreds** i Azure Time Series Insights.

## <a name="summary"></a>Sammanfattning

Varje Time Series Insights (TSD) har en inställning för att konfigurera **data lagrings tid**. Värdet sträcker sig från 1 till 400 dagar. Data tas bort baserat på miljöns lagrings kapacitet eller varaktigheten för kvarhållning (1-400), beroende på vilket som kommer först.

Varje TSD-miljö har ytterligare ett **gräns**värde för lagrings utrymme som har överskridits. Den här inställningen styr ingångs-och rensnings beteende när den maximala kapaciteten för en miljö nås. Du kan välja mellan två beteenden:

- **Rensa gamla data** objekt
- **Pausa ingress**

Detaljerad information för att bättre förstå de här inställningarna finns [i förstå kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Leta upp din befintliga Time Series Insights-miljö. Välj **alla resurser** på menyn på vänster sida av Azure Portal. Välj Time Series Insights-miljö.

1. Under rubriken **Inställningar** väljer du **Konfigurera**.

1. Välj **data lagrings tid** för att konfigurera kvarhållning med skjutreglaget eller Skriv ett tal i text rutan.

1. Observera **kapacitets** inställningen, eftersom den här konfigurationen påverkar den maximala mängden data händelser och den totala lagrings kapaciteten för att lagra data.

1. Växla inställningen för **lagrings gränsen överskreds** . Välj **Rensa gamla data** eller **pausa** inträngande beteende.

1. Konfigurera ändringarna genom att välja **Spara** .

## <a name="next-steps"></a>Nästa steg

- Mer information finns [i förstå kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).