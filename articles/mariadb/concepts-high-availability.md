---
title: Hög tillgänglighet - Azure Database för MariaDB
description: Det här avsnittet innehåller information om hög tillgänglighet när du använder Azure Database för MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a87646f6195a06cf0a5382cb248efa5516c953f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531999"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Begrepp med hög tillgänglighet i Azure Database för MariaDB
Azure Database för MariaDB-tjänsten ger en garanterad hög tillgänglighetsnivå. Det ekonomiskt stödda servicenivåavtalet (SLA) är 99,99 % vid allmän tillgänglighet. Det finns praktiskt taget ingen ner-tid när du använder den här tjänsten.

## <a name="high-availability"></a>Hög tillgänglighet
Ha-modellen (High Availability) baseras på inbyggda överlämningsmekanismer när ett avbrott på nodnivå inträffar. Ett avbrott på nodnivå kan uppstå på grund av ett maskinvarufel eller som svar på en tjänstdistribution.

Ändringar som görs i en Azure-databas för MariaDB-databasserver sker alltid i samband med en transaktion. Ändringar registreras synkront i Azure-lagring när transaktionen har genomförts. Om ett avbrott på nodnivå inträffar skapar databasservern automatiskt en ny nod och kopplar datalagring till den nya noden. Alla aktiva anslutningar tas bort och eventuella transaktioner ombord inte genomförs.

## <a name="application-retry-logic-is-essential"></a>Logik för återförsök för program är nödvändig
Det är viktigt att MariaDB-databasprogram är byggda för att identifiera och försöka bort tappade anslutningar och misslyckade transaktioner. När programmet försöker igen omdirigeras programmets anslutning transparent till den nyligen skapade instansen, som tar över för den misslyckade instansen.

Internt i Azure används en gateway för att omdirigera anslutningarna till den nya instansen. Vid ett avbrott tar hela fail-over-processen vanligtvis tiotals sekunder. Eftersom omdirigeringen hanteras internt av gatewayen förblir den externa anslutningssträngen densamma för klientprogrammen.

## <a name="scaling-up-or-down"></a>Skala upp eller ned
I likhet med HA-modellen skapas en ny serverinstans med den angivna storleken när en Azure-databas för MariaDB skalas upp eller ned. Den befintliga datalagringen är fristående från den ursprungliga instansen och kopplad till den nya instansen.

Under skalningsåtgärden inträffar ett avbrott i databasanslutningarna. Klientprogrammen kopplas från och öppna oengagerade transaktioner avbryts. När klientprogrammet försöker igen anslutningen eller gör en ny anslutning dirigerar gatewayen anslutningen till den nyligen dimensionerade instansen.

## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns i [Översikt över Azure Database för MariaDB](overview.md)
