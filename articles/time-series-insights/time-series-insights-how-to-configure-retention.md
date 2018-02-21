---
title: "Konfigurera kvarhållning i Azure tid serien Insights miljön | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar kvarhållning i Azure tid serien Insights-miljö."
services: time-series-insights
ms.service: time-series-insights
author: anshan
ms.author: anshan
manager: kfile
editor: MicrosoftDocs/tsidocs
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 02/09/2018
ms.openlocfilehash: bd688f516e200a37a6c88a8779282f7391eaf8b8
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="configuring-retention-in-time-series-insights"></a>Konfigurera bevaring i tid serien insikter
Den här artikeln beskriver hur du konfigurerar **datalagringstid** och **lagringsgräns överskred beteende** i Azure tid serien insikter.

Varje gång serien insikter (TSD) miljöer har inställningen för att konfigurera **datalagringstid**. Värdet ska vara mellan 1 och 400 dagar. Data tas bort baserat på miljön lagring kapacitet eller kvarhållande varaktigheten (1-400), beroende på vilket som inträffar först.

Varje TSD-miljö har en inställning för ytterligare **lagringsgräns överskred beteende**. Den här inställningen styr beteendet för inkommande trafik och rensa när maxkapaciteten för en miljö har uppnåtts. Det finns två beteenden kan välja mellan:
- **Radera gamla data** (standard)  
- **Pausa ingång**

Detaljerad information för att bättre förstå dessa inställningar, granska [förstå kvarhållning i tid serien insikter](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Konfigurera datalagring

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta upp din befintliga miljö tid serien insikter. Välj **alla resurser** på menyn till vänster i Azure-portalen. Välj Time Series Insights-miljö.

3. Under den **inställningar** rubrik, Välj **konfigurera**.

4. Välj den **datalagringstid** att konfigurera kvarhållning med hjälp av skjutreglaget eller Skriv ett värde i textrutan.

5. Observera den **kapacitet** anger, eftersom den här konfigurationen påverkar den maximala mängden Datahändelser och lagringskapacitet för att lagra data. 

6. Växla den **lagringsgräns överskred beteende** inställningen. Välj **Radera gamla data** eller **pausa ingång** beteende.

7. Välj **spara** konfigurera ändringarna.

## <a name="next-steps"></a>Nästa steg
Mer information hittar [förstå kvarhållning i tid serien insikter](time-series-insights-concepts-retention.md).
