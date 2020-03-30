---
title: Metodtips för Query Store i Azure Database for PostgreSQL - Single Server
description: I den här artikeln beskrivs metodtips för Query Store i Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70764224"
---
# <a name="best-practices-for-query-store"></a>Metodtips för Query Store

**Gäller:** Azure-databas för PostgreSQL - Single Server version 9.6, 10, 11

I den här artikeln beskrivs metodtips för att använda Query Store i Azure Database för PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Ange det optimala frågeinsamlingsläget
Låt Query Store samla in de data som är viktiga för dig. 

|**pg_qs.query_capture_mode** | **Scenario**|
|---|---|
|_Alla_  |Analysera din arbetsbelastning noggrant när det gäller alla frågor och deras körningsfrekvenser och annan statistik. Identifiera nya frågor i din arbetsbelastning. Identifiera om ad hoc-frågor används för att identifiera möjligheter för användar- eller automatisk parameterisering. _Alla_ levereras med en ökad resursförbrukning kostnad. |
|_Överst på sidan_  |Fokusera din uppmärksamhet på topp frågor - de som utfärdats av kunder.
|_Inget_ |Du har redan tagit in en frågeuppsättning och ett tidsfönster som du vill undersöka och du vill eliminera de distraktioner som andra frågor kan medföra. _Ingen_ är lämplig för provning och bänkmärkning miljöer. _Ingen_ bör användas med försiktighet eftersom du kan missa möjligheten att spåra och optimera viktiga nya frågor. Du kan inte återställa data på de senaste tidsfönstren. |

Query Store innehåller också en butik för väntestatistik. Det finns ytterligare en fråga om insamlingsläge som styr väntestatistik: **pgms_wait_sampling.query_capture_mode** kan ställas in på _ingen_ eller _alla_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** ersätter **pgms_wait_sampling.query_capture_mode**. Om pg_qs.query_capture_mode _inte_finns har inställningen pgms_wait_sampling.query_capture_mode ingen effekt. 


## <a name="keep-the-data-you-need"></a>Behåll de data du behöver
Parametern **pg_qs.retention_period_in_days** anger i dagar datalagringsperioden för Query Store. Äldre fråge- och statistikdata tas bort. Som standard är Query Store konfigurerat för att behålla data i 7 dagar. Undvik att behålla historiska data som du inte planerar att använda. Öka värdet om du behöver behålla data längre.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Ange frekvensen för provtagning av väntestatistik 
Parametern **pgms_wait_sampling.history_period** anger hur ofta (i millisekunder) väntar händelser. Ju kortare period, desto oftare provtagning. Mer information hämtas, men det kommer med kostnaden för större resursförbrukning. Öka den här perioden om servern är under belastning eller om du inte behöver granulariteten


## <a name="get-quick-insights-into-query-store"></a>Få snabba insikter i Query Store
Du kan använda [Query Performance Insight](concepts-query-performance-insight.md) i Azure-portalen för att få snabbinformation om data i Query Store. Visualiseringarna visas de längsta frågorna och de längsta väntetiderna över tid.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du hämtar eller anger parametrar med Hjälp av [Azure-portalen](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).