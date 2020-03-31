---
title: Hög tillgänglighet - Azure Database för PostgreSQL - Single Server
description: Den här artikeln innehåller information om hög tillgänglighet i Azure Database för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 80229ff78c4570db583f1218d5d2f72da2dec388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768579"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Begrepp med hög tillgänglighet i Azure Database för PostgreSQL - Single Server
Azure Database for PostgreSQL-tjänsten ger en garanterad hög tillgänglighetsnivå. Det ekonomiskt stödda servicenivåavtalet (SLA) är 99,99 % vid allmän tillgänglighet. Det finns praktiskt taget ingen ner-tid när du använder den här tjänsten.

## <a name="high-availability"></a>Hög tillgänglighet
Ha-modellen (High Availability) baseras på inbyggda redundansmekanismer när ett avbrott på nodnivå inträffar. Ett avbrott på nodnivå kan uppstå på grund av ett maskinvarufel eller som svar på en tjänstdistribution.

Ändringar som görs i en Azure-databas för PostgreSQL-databasserver sker alltid i samband med en transaktion. Ändringar registreras synkront i Azure-lagring när transaktionen har genomförts. Om ett avbrott på nodnivå inträffar skapar databasservern automatiskt en ny nod och kopplar datalagring till den nya noden. Alla aktiva anslutningar tas bort och eventuella transaktioner ombord inte genomförs.

## <a name="application-retry-logic-is-essential"></a>Logik för återförsök för program är nödvändig
Det är viktigt att PostgreSQL databasprogram är byggda för att upptäcka och försöka bort tappade anslutningar och misslyckade transaktioner. När programmet försöker igen omdirigeras programmets anslutning transparent till den nyligen skapade instansen, som tar över för den misslyckade instansen.

Internt i Azure används en gateway för att omdirigera anslutningarna till den nya instansen. Vid ett avbrott tar hela fail-over-processen vanligtvis tiotals sekunder. Eftersom omdirigeringen hanteras internt av gatewayen förblir den externa anslutningssträngen densamma för klientprogrammen.

## <a name="scaling-up-or-down"></a>Skala upp eller ned
I likhet med HA-modellen skapas en ny serverinstans med den angivna storleken när en Azure-databas för PostgreSQL skalas upp eller ned. Den befintliga datalagringen är fristående från den ursprungliga instansen och kopplad till den nya instansen.

Under skalningsåtgärden inträffar ett avbrott i databasanslutningarna. Klientprogrammen kopplas från och öppna oengagerade transaktioner avbryts. När klientprogrammet försöker igen anslutningen eller gör en ny anslutning dirigerar gatewayen anslutningen till den nyligen dimensionerade instansen. 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [hur du hanterar tillfälliga anslutningsfel](concepts-connectivity.md)
- Lär dig hur du [replikerar dina data med läsrepliker](howto-read-replicas-portal.md)
