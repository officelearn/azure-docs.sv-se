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
ms.date: 08/25/2020
ms.openlocfilehash: 85459f357032a7f9944d50e3e4f3929015c6dcfd
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869125"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Planera för Azures underhålls händelser i Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Lär dig hur du förbereder för planerade underhålls händelser på din databas i Azure SQL Database och Azure SQL-hanterad instans.

## <a name="what-is-a-planned-maintenance-event"></a>Vad är ett planerat underhålls evenemang?

För varje databas upprätthåller Azure SQL Database och Azure SQL-hanterad instans ett kvorum med databas repliker där en replik är primär. Vid alla tillfällen måste en primär replik vara Onlineunderhåll och minst en sekundär replik måste vara felfri. Under planerat underhåll kommer medlemmar i databasobjektet att försättas i taget en i taget, med avsikt att det finns en svarande primär replik och minst en sekundär replik online för att säkerställa att inga klient avbrott uppstår. När den primära repliken måste försättas i offlineläge görs en omkonfigurations-/redundansväxling i vilken en sekundär replik blir den nya primära repliken.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Vad som ska förväntas under en planerad underhålls händelse

Underhålls händelser kan producera enstaka eller flera redundans, beroende på Constellation för de primära och sekundära replikerna i början av underhålls händelsen. I genomsnitt sker 1,7 redundans per planerat underhålls evenemang. Omkonfigurationer/redundans slutförs vanligt vis inom 30 sekunder. Genomsnittet är 8 sekunder. Om det redan är anslutet måste ditt program återansluta till den nya primära repliken av databasen. Om en ny anslutning görs medan databasen genomgår en omkonfiguration innan den nya primära repliken är online får du fel 40613 (databasen är inte tillgänglig): *"databasen {databasename} på servern {servername} är inte tillgänglig för tillfället. Försök att ansluta igen senare. "* Om din databas har en tids krävande fråga avbryts den här frågan under en omkonfiguration och måste startas om.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>Så här simulerar du ett planerat underhålls evenemang

Att se till att klient programmet är elastiskt för underhålls händelser innan distribution till produktion bidrar till att minska risken för program fel och kommer att bidra till programmets tillgänglighet för slutanvändarna. Du kan testa beteendet för klient programmet under planerade underhålls händelser genom att [initiera manuell redundansväxling](https://aka.ms/mifailover-techblog) via POWERSHELL, CLI eller REST API. Den genererar samma beteende som underhålls händelse som tar den primära repliken offline.

## <a name="retry-logic"></a>Logik för omprövning

Alla klient produktions program som ansluter till en moln databas tjänst bör implementera en robust logik för anslutnings [försök](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors). Detta hjälper till att göra redundansväxlingen transparent för slutanvändarna eller minst minimera negativa effekter.

## <a name="resource-health"></a>Resurshälsa

Om det uppstår inloggnings problem i databasen, kontrollerar du [Resource Health](../../service-health/resource-health-overview.md#get-started) -fönstret i [Azure Portal](https://portal.azure.com) för aktuell status. Avsnittet hälso historik innehåller avbrotts orsaken för varje händelse (om tillgänglig).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Resource Health](resource-health-to-troubleshoot-connectivity.md) för Azure SQL Database och Azure SQL-hanterad instans.
- Mer information om logik för omprövning finns i avsnittet [om omprövnings logik för tillfälliga fel](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
