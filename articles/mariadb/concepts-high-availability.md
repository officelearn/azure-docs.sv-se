---
title: Begrepp för hög tillgänglighet i Azure Database for MariaDB
description: Det här avsnittet innehåller information om hög tillgänglighet med Azure Database for MariaDB
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 2f06c9515c6ee79ea7475452b7000d1babdbecfe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956531"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Begrepp för hög tillgänglighet i Azure Database for MariaDB
Tjänsten Azure Database for MariaDB tillhandahåller garanterat hög tillgänglighet. Ekonomisk servicenivåavtalet (SLA) är 99,99% vid allmän tillgänglighet. SERVICEAVTALET gäller inte för offentlig förhandsversion. Det är praktiskt taget ingen program driftstopp när du använder den här tjänsten.

## <a name="high-availability"></a>Hög tillgänglighet
Modell med hög tillgänglighet (HA) baseras på inbyggda mekanismer för redundans när en nod på servernivå avbrott inträffar. Ett avbrott för noden på servernivå kan inträffa på grund av ett maskinvarufel eller som svar på en tjänstdistribution.

Vid alla tidpunkter uppstå ändringar som gjorts i en Azure Database for MariaDB-databasservern i samband med en transaktion. Ändringar sparas synkront i Azure storage när transaktionen genomförs. Om en nod på servernivå avbrott inträffar, databasservern automatiskt skapar en ny nod och lagring av data till den nya noden. Alla aktiva anslutningar tas bort och alla aktiva transaktioner genomförs inte.

## <a name="application-retry-logic-is-essential"></a>Logik för omprövning av program som är viktigt
Det är viktigt att MariaDB databasprogram har skapats för att identifiera och försök avbrutna anslutningar och misslyckats transaktioner. När programmet försöker omdirigeras transparent programmets anslutning till den nya instansen, som tar för den felaktiga instansen.

Internt i Azure används en gateway för att omdirigera anslutningar till den nya instansen. Redundansväxla hela processen tar normalt tio sekunder eller vid ett avbrott. Eftersom omdirigeringen hanteras internt av gatewayen, detsamma externa anslutningssträngen för klientprogram.

## <a name="scaling-up-or-down"></a>Skala upp eller ned
Liknar modellen hög tillgänglighet när en Azure Database for MariaDB skalas upp eller ned, en ny server-instansen med den angivna storleken skapas. Befintliga datalagring är frånkopplat från den ursprungliga instansen och kopplat till den nya instansen.

Under åtgärden sker ett avbrott i databasanslutningar. Klientprogrammen är frånkopplade och öppna ogenomförda transaktioner har avbrutits. När klientprogrammet försöker ansluta igen, eller gör en ny anslutning, dirigeras gatewayen anslutningen till den nya storlekar instansen.

## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns i [Azure Database for MariaDB-översikt](overview.md)
