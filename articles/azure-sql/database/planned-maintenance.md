---
title: Planera för underhålls händelser i Azure
description: Lär dig hur du förbereder för planerade underhålls händelser i Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 5bdc3eb8c118c19f90ce1fd92ac5ee156719dacd
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987232"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Planera för Azures underhålls händelser i Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Lär dig hur du förbereder för planerade underhålls händelser på din databas i Azure SQL Database och Azure SQL-hanterad instans.

## <a name="what-is-a-planned-maintenance-event"></a>Vad är ett planerat underhålls evenemang?

För varje databas upprätthåller Azure SQL Database och Azure SQL-hanterad instans ett kvorum med databas repliker där en replik är primär. Vid alla tillfällen måste en primär replik vara Onlineunderhåll och minst en sekundär replik måste vara felfri. Under planerat underhåll kommer medlemmar i databasobjektet att försättas i taget en i taget, med avsikt att det finns en svarande primär replik och minst en sekundär replik online för att säkerställa att inga klient avbrott uppstår. När den primära repliken måste försättas i offlineläge görs en omkonfigurations-/redundansväxling i vilken en sekundär replik blir den nya primära repliken.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Vad som ska förväntas under en planerad underhålls händelse

Omkonfigurationer/redundans slutförs vanligt vis inom 30 sekunder. Genomsnittet är 8 sekunder. Om det redan är anslutet måste ditt program återansluta till den felfria kopian av databasen. Om en ny anslutning görs medan databasen genomgår en omkonfiguration innan den nya primära repliken är online får du fel 40613 (databasen är inte tillgänglig): "databasen {databasename} på servern {servername} är inte tillgänglig för tillfället. Gör ett nytt försök med anslutningen senare". Om din databas har en tids krävande fråga avbryts den här frågan under en omkonfiguration och måste startas om.

## <a name="retry-logic"></a>Logik för omprövning

Alla klient produktions program som ansluter till en moln databas tjänst bör implementera en robust logik för anslutnings [försök](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors). Detta hjälper till att minimera dessa situationer och bör vanligt vis göra felen transparent för slutanvändaren.

## <a name="frequency"></a>Frekvens

I genomsnitt sker 1,7 planerade underhålls händelser varje månad.

## <a name="resource-health"></a>Resurshälsa

Om det uppstår inloggnings problem i databasen, kontrollerar du [Resource Health](../../service-health/resource-health-overview.md#get-started) -fönstret i [Azure Portal](https://portal.azure.com) för aktuell status. Avsnittet hälso historik innehåller avbrotts orsaken för varje händelse (om tillgänglig).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Resource Health](resource-health-to-troubleshoot-connectivity.md) för Azure SQL Database och Azure SQL-hanterad instans.
- Mer information om logik för omprövning finns i avsnittet [om omprövnings logik för tillfälliga fel](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
