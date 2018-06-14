---
title: Planerat underhåll - Azure SQL Data Warehouse | Microsoft Docs
description: Lär dig hur du förbereder för planerat underhållshändelser till din Azure SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a273ad4c256267865d3af324f0ef755a6cb75c5c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31800057"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Planera för underhåll på ditt Azure SQL data warehouse

Lär dig hur du förbereder för planerat underhållshändelser på ditt Azure SQL data warehouse.

## <a name="what-is-a-planned-maintenance-event"></a>Vad är en planerad underhållshändelse?
En planerad underhållshändelse är en tidsperiod när ditt data warehouse måste vara offline för underhåll. Dessa händelser finns möjligheter för att använda tjänsten uppgraderingar, nya funktioner och korrigeringar. 

## <a name="frequency"></a>Frekvens
Minst en planerat underhållshändelse inträffar i genomsnitt varje månad. 

## <a name="notifications-and-downtime"></a>Meddelanden och driftstopp
Du får ett meddelande innan varje planerad underhållshändelse. Underhåll händelsen avbryts alla frågor som körs och tar ditt data warehouse offline. Förväntad avbrottstid för varje data warehouse är cirka 30 minuter. Du kan förvänta dig ett meddelande när Underhåll är klar. 

## <a name="setting-up-alerts"></a>Konfigurera aviseringar

Vi rekommenderar att du använder [Azure-Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) att ställa in planerat underhåll loggen aviseringar. Aviseringarna kan hjälpa dig planera för det nödvändiga underhållet att minimera påverkan på din verksamhet. 

Om du vill ställa in meddelanden, kan du använda dessa [logga avisering instruktioner](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## <a name="next-steps"></a>Nästa steg
Mer information om övervakning finns [övervaka din arbetsbelastning](sql-data-warehouse-manage-monitor.md).
