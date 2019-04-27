---
title: Begrepp för hög tillgänglighet i Azure Database for PostgreSQL
description: Den här artikeln innehåller information för hög tillgänglighet med Azure Database för PostgreSQL.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 02/01/2019
ms.date: 02/18/2019
ms.openlocfilehash: d43647b57469efa4581dc1c74a842a51e9d54ad7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623824"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql"></a>Begrepp för hög tillgänglighet i Azure Database for PostgreSQL
Tjänsten Azure Database for PostgreSQL ger garanterat hög tillgänglighet. Ekonomisk servicenivåavtalet (SLA) är 99,99% vid allmän tillgänglighet. Det är praktiskt taget ingen program driftstopp när du använder den här tjänsten.

## <a name="high-availability"></a>Hög tillgänglighet
Modell med hög tillgänglighet (HA) baseras på mekanismer för inbyggd växling vid fel när en nod på servernivå avbrott inträffar. Ett avbrott för noden på servernivå kan inträffa på grund av ett maskinvarufel eller som svar på en tjänstdistribution.

Vid alla tidpunkter uppstå ändringar som gjorts i en Azure Database for PostgreSQL-databasservern i samband med en transaktion. Ändringar sparas synkront i Azure storage när transaktionen genomförs. Om en nod på servernivå avbrott inträffar, databasservern automatiskt skapar en ny nod och lagring av data till den nya noden. Alla aktiva anslutningar tas bort och alla aktiva transaktioner genomförs inte.

## <a name="application-retry-logic-is-essential"></a>Logik för omprövning av program som är viktigt
Det är viktigt att PostgreSQL databasprogram har skapats för att identifiera och försök avbrutna anslutningar och misslyckats transaktioner. När programmet försöker omdirigeras transparent programmets anslutning till den nya instansen, som tar för den felaktiga instansen.

Internt i Azure används en gateway för att omdirigera anslutningar till den nya instansen. Redundansväxla hela processen tar normalt tio sekunder eller vid ett avbrott. Eftersom omdirigeringen hanteras internt av gatewayen, detsamma externa anslutningssträngen för klientprogram.

## <a name="scaling-up-or-down"></a>Skala upp eller ned
Liknar modellen hög tillgänglighet när en Azure Database for PostgreSQL skalas upp eller ned, en ny server-instansen med den angivna storleken skapas. Befintliga datalagring är frånkopplat från den ursprungliga instansen och kopplat till den nya instansen.

Under åtgärden sker ett avbrott i databasanslutningar. Klientprogrammen är frånkopplade och öppna ogenomförda transaktioner har avbrutits. När klientprogrammet försöker ansluta igen, eller gör en ny anslutning, dirigeras gatewayen anslutningen till den nya storlekar instansen. 

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [hantering av tillfälliga anslutningsfel](concepts-connectivity.md)
- Lär dig hur du [replikera dina data med skrivskyddade repliker](howto-read-replicas-portal.md)
