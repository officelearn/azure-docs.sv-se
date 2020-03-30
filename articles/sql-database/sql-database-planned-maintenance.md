---
title: Planera för Underhållshändelser för Azure
description: Lär dig hur du förbereder dig för planerade underhållshändelser till din Azure SQL-databas.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821308"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planera för Azure-underhållshändelser i Azure SQL Database

Lär dig hur du förbereder dig för planerade underhållshändelser i din Azure SQL-databas.

## <a name="what-is-a-planned-maintenance-event"></a>Vad är en planerad underhållshändelse

För varje databas upprätthåller Azure SQL DB kvorum av databasrepliker där en replik är den primära. Hela tiden måste en primär replik vara online service och minst en sekundär replik måste vara felfri. Under planerat underhåll kommer medlemmar i databasens kvorum att gå offline en i taget, med avsikten att det finns en svarande primär replik och minst en sekundär replik online för att säkerställa att inga klientstopp. När den primära repliken måste kopplas från, sker en omkonfiguration/redundansprocess där en sekundär replik blir den nya primära.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Vad du kan förvänta dig under en planerad underhållshändelse

Omkonfigurationer/redundans slutförs i allmänhet inom 30 sekunder – medelvärdet är 8 sekunder. Om programmet redan är anslutet måste det återansluta till den felfria kopian av en ny primär replik i databasen. Om en ny anslutning försöker när databasen genomgår en omkonfiguration innan den nya primära repliken är online, visas fel 40613 (Databasen är inte tillgänglig): "Databasen {databasnamn} på servern {servernamn} är inte tillgänglig för tillfället. Försök igen anslutningen senare.". Om databasen har en tidskrävande fråga avbryts den här frågan under en omkonfiguration och måste startas om.

## <a name="retry-logic"></a>Logik för återförsök

Alla klientproduktionsprogram som ansluter till en molndatabastjänst bör implementera en robust [logik för återförsök](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)för anslutning . Detta kommer att bidra till att minska dessa situationer och bör i allmänhet göra felen transparenta för slutanvändaren.

## <a name="frequency"></a>Frequency

I genomsnitt inträffar 1,7 planerade underhållshändelser varje månad.

## <a name="resource-health"></a>Resource Health

Om din SQL-databas har inloggningsfel kontrollerar du fönstret [Resurshälsa](../service-health/resource-health-overview.md#get-started) i [Azure-portalen](https://portal.azure.com) för aktuell status. Avsnittet Hälsohistorik innehåller orsaken till driftstopp för varje händelse (när den är tillgänglig).


## <a name="next-steps"></a>Nästa steg

- Läs mer om [Resurshälsa](sql-database-resource-health.md) för SQL Database
- Mer information om logik för återförsök finns i [Logik för återförsök för tillfälliga fel](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
