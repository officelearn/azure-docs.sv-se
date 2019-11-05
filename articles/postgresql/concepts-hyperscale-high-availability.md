---
title: Hög tillgänglighet i Azure Database for PostgreSQL – storskalig (citus)
description: Koncept för hög tillgänglighet och haveri beredskap
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 616b5bff735f52d137c12c58ac6023c38a2d4044
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514750"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Hög tillgänglighet i Azure Database for PostgreSQL – storskalig (citus)

Hög tillgänglighet (HA) förhindrar databas stillestånd genom att underhålla vänte repliker för varje nod i en Server grupp. Om en nod kraschar växlar storskaligt inkommande anslutningar från den felande noden till vänte läge. Redundansväxlingen sker inom några minuter och de upphöjda noderna har alltid nya data genom PostgreSQL synkron strömmande replikering.

För att kunna dra nytta av HA på koordinator-noden måste databas programmen identifiera och försöka ta bort anslutningar på nytt och misslyckade transaktioner. Den nyligen framhävda koordinatorn kommer att vara tillgänglig med samma anslutnings sträng.

Återställningen kan delas upp i tre steg: identifiering, redundans och fullständig återställning.  Storskalig körning av regelbundna hälso kontroller på varje nod, och efter fyra misslyckade kontroller avgör det att en nod är nere. Storskaligt höjer sedan ett vänte läge till den primära nodens status (redundans) och etablerar en ny vänte läge.
Streaming-replikering börjar och den nya noden aktive ras.  När alla data har repliker ATS har noden nått fullständig återställning.

### <a name="next-steps"></a>Nästa steg

- Lär dig hur du [aktiverar hög tillgänglighet](howto-hyperscale-high-availability.md) i en storskalig Server grupp.
