---
title: Planerat underhåll – Azure SQL Data Warehouse | Microsoft Docs
description: Lär dig hur du förbereder för planerade underhållshändelser till din Azure SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 8b5b94c959ae9820cab338f3ee3317fb3ee166d6
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286609"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Planera för underhåll i Azure SQL data warehouse

Lär dig hur du förbereder för planerade underhållshändelser i Azure SQL data warehouse.

## <a name="what-is-a-planned-maintenance-event"></a>Vad är en planerad underhållshändelse?
En planerad underhållshändelse är en tidsperiod när ditt informationslager måste vara offline för underhåll. Dessa händelser finns möjligheter för att tillämpa service uppgraderingar, nya funktioner och korrigeringar. 

## <a name="frequency"></a>Frekvens
Minst en planerad underhållshändelse inträffar i genomsnitt varje månad. 

## <a name="notifications-and-downtime"></a>Meddelanden och driftstopp
Du får ett meddelande innan varje händelse som planerat underhåll. Händelsen Underhåll avbryter alla frågor som körs och tar ditt informationslager offline. Förväntad avbrottstid för varje datalager är cirka 30 minuter. Du kan förvänta dig ett meddelande när Underhåll är klar. 

## <a name="setting-up-alerts"></a>Ställa in aviseringar

Vi rekommenderar att du använder [Azure Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) att ställa in planerat underhåll loggaviseringar. Aviseringarna kan hjälpa dig att planera för det nödvändiga underhållet att minimera inverkan på din verksamhet. 

Om du vill konfigurera meddelanden kan du använda dessa [logga avisering instruktioner](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du övervakar [övervaka din arbetsbelastning](sql-data-warehouse-manage-monitor.md).
