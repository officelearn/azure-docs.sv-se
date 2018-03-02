---
title: "Hög tillgänglighet koncept i Azure för MySQL-databas"
description: "Det här avsnittet innehåller information för hög tillgänglighet när du använder Azure-databas för MySQL"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 58f10f0a0938da5887d44959f65b9016dfa88f2c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Hög tillgänglighet koncept i Azure för MySQL-databas
Azure-databas för MySQL-tjänst som tillhandahåller en garanterad hög tillgänglighet. Ekonomiskt säkerhetskopieras servicenivåavtalet (SLA) är 99,99% vid allmän tillgänglighet. Det är praktiskt taget inget program stillestånd när du använder den här tjänsten.

## <a name="high-availability"></a>Hög tillgänglighet
Hög tillgänglighet (HA)-modellen baseras på inbyggda failover-funktioner när en nod på objektnivå avbrott inträffar. Ett avbrott nivån kan inträffa på grund av ett maskinvarufel eller som svar på en tjänstdistribution.

Vid alla tidpunkter uppstå ändringar som gjorts i en Azure-databas för MySQL-databasservern i samband med en transaktion. Ändringar registreras i Azure storage synkront när genomförs transaktionen. Om en nod på objektnivå avbrott inträffar databasservern automatiskt skapar en ny nod och lagring av data till den nya noden. Alla aktiva anslutningar tas bort och alla inflight transaktioner genomförs inte.

## <a name="application-retry-logic-is-essential"></a>Logik för omprövning av programmet krävs
Det är viktigt att MySQL databasprogram har skapats för att identifiera och försök avbrutna anslutningar och inte kunde transaktioner. När programmet försöker omdirigeras transparent programmets anslutning till den nyligen skapade instansen, som tar för instansen som misslyckats.

Internt i Azure används en gateway för att dirigera om anslutningar till den nya instansen. Vid ett avbrott ta failover hela processen brukar flera sekunder. Eftersom omdirigeringen hanteras internt av gateway, densamma externa anslutningssträngen för klientprogrammen.

## <a name="scaling-up-or-down"></a>Skala upp eller ned
Liknar modellen hög tillgänglighet, när en Azure-databas för MySQL skalas upp eller ned, en ny serverinstans med den angivna storleken skapas. Befintliga datalagring är frånkopplat från den ursprungliga instansen och anslutna till den nya instansen.

Under åtgärden sker ett avbrott databasanslutningarna. Klientprogrammen är frånkopplad och öppna ogenomförda transaktioner har avbrutits. När klientprogrammet försöker upprätta anslutningen, eller gör en ny anslutning, dirigerar gatewayen anslutningen till nyligen storlek instansen. 

## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns [Azure Database MySQL-översikt](overview.md)
