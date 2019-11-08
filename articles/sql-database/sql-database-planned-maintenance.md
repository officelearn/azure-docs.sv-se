---
title: Planera för underhålls händelser i Azure
description: Lär dig hur du förbereder för planerade underhålls händelser till din Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: ba882176fbe17f7b74c786f421dde8fadd58d9b7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821308"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planera för Azures underhålls händelser i Azure SQL Database

Lär dig hur du förbereder för planerade underhålls händelser på din Azure SQL-databas.

## <a name="what-is-a-planned-maintenance-event"></a>Vad är ett planerat underhålls evenemang

För varje databas upprätthåller Azure SQL DB ett kvorum med databas repliker där en replik är primär. Vid alla tillfällen måste en primär replik vara Onlineunderhåll och minst en sekundär replik måste vara felfri. Under planerat underhåll kommer medlemmar i databasobjektet att försättas i taget en i taget, med avsikt att det finns en svarande primär replik och minst en sekundär replik online för att säkerställa att inga klient avbrott uppstår. När den primära repliken måste försättas i offlineläge görs en omkonfigurations-/redundansväxling i vilken en sekundär replik blir den nya primära repliken.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Vad som ska förväntas under en planerad underhålls händelse

Omkonfigurationer/redundans slutförs vanligt vis inom 30 sekunder – genomsnittet är 8 sekunder. Om det redan är anslutet måste ditt program återansluta till den felfria kopian av databasen. Om en ny anslutning görs medan databasen genomgår en omkonfiguration innan den nya primära repliken är online får du fel 40613 (databasen är inte tillgänglig): "databasen {databasename} på servern {servername} är inte tillgänglig för tillfället. Försök att ansluta igen senare. ". Om din databas har en tids krävande fråga avbryts den här frågan under en omkonfiguration och måste startas om.

## <a name="retry-logic"></a>Omprövnings logik

Alla klient produktions program som ansluter till en moln databas tjänst bör implementera en robust logik för anslutnings [försök](sql-database-connectivity-issues.md#retry-logic-for-transient-errors). Detta hjälper till att minimera dessa situationer och bör vanligt vis göra felen transparent för slutanvändaren.

## <a name="frequency"></a>Frequency

I genomsnitt sker 1,7 planerade underhålls händelser varje månad.

## <a name="resource-health"></a>Resource Health

Om SQL-databasen har inloggnings problem kontrollerar du [Resource Healths](../service-health/resource-health-overview.md#get-started) fönstret i [Azure Portal](https://portal.azure.com) för aktuell status. Avsnittet hälso historik innehåller avbrotts orsaken för varje händelse (om tillgänglig).


## <a name="next-steps"></a>Nästa steg

- Läs mer om [Resource Health](sql-database-resource-health.md) för SQL Database
- Mer information om logik för omprövning finns i [omprövnings logik för tillfälliga fel](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
