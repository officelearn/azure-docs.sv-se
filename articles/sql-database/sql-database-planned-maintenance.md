---
title: Planera för Azure-Underhåll händelser – Azure SQL Database | Microsoft Docs
description: Lär dig hur du förbereder för planerade underhållshändelser till din Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 928338a911efae051df7164239dbd19f9317338a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824316"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planering för Azure-Underhåll i Azure SQL Database

Lär dig hur du förbereder planerat underhåll utförs på din Azure SQL-databas.

## <a name="what-is-a-planned-maintenance-event"></a>Vad är en planerad underhållshändelse

För varje databas upprätthåller Azure SQL DB ett kvorum av databasrepliker där en replik är primärt. AT alltid en primär replik måste vara online Service och minst en sekundär replik måste vara felfritt. Under planerat underhåll går medlemmar i databasen kvorum offline en i taget, med avsikt att det finns en svarar primära repliken och minst en sekundär replik online så utan avbrott för klienten. När den primära repliken behöver tas offline, sker en omkonfiguration/redundans-process som en sekundär replik blir den nya primärt.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Vad som händer under en planerad underhållshändelse

Reconfigurations/redundans Allmänt slutförs inom 30 sekunder – medelvärdet är 8 sekunder. Om redan anslutit måste ditt program återansluta till felfria kopia ny primär replik av databasen. Om en ny anslutning görs när databasen genomgår en omkonfiguration innan den nya primära repliken är online, får du fel 40613 (databasen är inte tillgänglig): ”Databasen {databasename} på servern {servername} är inte tillgänglig för tillfället. Försök med anslutningen senare ”. Om din databas har en tidskrävande fråga kan den här frågan avbryts under en omkonfiguration och måste startas om.

## <a name="retry-logic"></a>Logik för omprövning

Produktion klientprogram som ansluter till en molndatabastjänst bör implementera en robust anslutning [logik för omprövning](sql-database-connectivity-issues.md#retry-logic-for-transient-errors). Detta hjälper att minimera sådana situationer och bör Allmänt se felen transparent för slutanvändaren.

## <a name="frequency"></a>Frekvens

1.7 planerat underhållshändelser inträffar i genomsnitt varje månad.

## <a name="resource-health"></a>Resource Health

Om din SQL-databas har drabbats av misslyckade inloggningar, kontrollerar du den [Resource Health](../service-health/resource-health-overview.md#getting-started) fönstret i den [Azure-portalen](https://portal.azure.com) för aktuell status. Avsnittet Hälsohistorik innehåller driftstopp orsaken för varje händelse (i förekommande fall).


## <a name="next-steps"></a>Nästa steg

- Läs mer om [Resource Health](sql-database-resource-health.md) för SQL-databas
- Läs mer om logik för omprövning, [logik för omprövning tillfälliga fel](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
