---
title: Query Store bästa praxis i Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskriver Metodtips för Query Store i Azure Database för PostgreSQL – enskild Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 798a7a3edbf11c8421848871d26ba55b5bada0b6
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067243"
---
# <a name="best-practices-for-query-store"></a>Metodtips för Query Store

**Gäller för:** Azure Database för PostgreSQL – enskild Server 9.6 och 10

Den här artikeln beskriver de bästa metoderna för att använda Query Store i Azure Database för PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Ställ in optimala frågeprestanda bildtagningsläge
Låter Query Store hämtade data som är viktiga för dig. 

|**pg_qs.query_capture_mode** | **Scenario**|
|---|---|
|_Alla_  |Analysera din arbetsbelastning noggrant vad gäller alla frågor och körningen frekvensen och annan statistik. Identifiera nya frågor i din arbetsbelastning. Identifiera om ad hoc-frågor för att identifiera möjligheter för användare eller automatisk parameterisering. _Alla_ levereras med en ökad resursförbrukning kostnad. |
|_längst upp_  |Fokusera din uppmärksamhet på de viktigaste frågorna - de utfärdade av klienter.
|_Ingen_ |Du redan har hämtat en fråga uppsättning och tidsperioden som du vill undersöka och du vill undvika störningar som andra frågor kan införa. _Ingen_ är lämplig för testning och prestandamått miljöer. _Ingen_ ska användas med försiktighet eftersom du kan gå miste om möjlighet att spåra och optimera viktiga nya frågor. Du kan inte återställa data på de senaste tidsfönster. |

Query Store innehåller också ett Arkiv för wait-statistik. Det finns en fråga i ytterligare avbildning som styr vänta statistik: **pgms_wait_sampling.query_capture_mode** kan anges till _ingen_ eller _alla_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** supersedes **pgms_wait_sampling.query_capture_mode**. Om pg_qs.query_capture_mode är _ingen_, pgms_wait_sampling.query_capture_mode-inställningen har ingen effekt. 


## <a name="keep-the-data-you-need"></a>Behåll de data du behöver
Den **pg_qs.retention_period_in_days** parametern anger i dagar Datalagringsperiod för Query Store. Äldre fråge- och statistik data tas bort. Query Store har konfigurerats för att kvarhålla data i sju dagar som standard. Undvik att hålla historiska data som du inte tänker använda. Öka värdet om du vill behålla data längre.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Ange frekvensen för sampling-väntestatistik 
Den **pgms_wait_sampling.history_period** parametern anger hur ofta (i millisekunder) samplas vänta händelser. Ju kortare period, oftare desto är samplingen. Hämta mer information, men som medföljer kostnaden för större resursförbrukning. Öka den här perioden om servern är under belastning eller om du inte behöver precisionen


## <a name="get-quick-insights-into-query-store"></a>Få snabba insikter om Query Store
Du kan använda [Query Performance Insight](concepts-query-performance-insight.md) i Azure portal för att få snabba insikter om data i Query Store. Visualiseringar lyfta fram de längsta frågorna som körs och vänta flest händelser över tid.

## <a name="next-steps"></a>Nästa steg
- Lär dig att hämta eller ange parametrar med den [Azure-portalen](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).