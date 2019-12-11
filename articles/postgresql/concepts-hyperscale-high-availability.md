---
title: Hög tillgänglighet – storskalig (citus) – Azure Database for PostgreSQL
description: Koncept för hög tillgänglighet och haveri beredskap
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975541"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Hög tillgänglighet i Azure Database for PostgreSQL – storskalig (citus)

Hög tillgänglighet (HA) förhindrar avbrottstid i databaser genom att upprätthålla standbyrepliker för varje nod i en servergrupp. Om en nod slutar fungera växlar Hyperskala inkommande anslutningar från den felaktiga noden till dess standbyreplik. Redundansväxling sker inom ett par minuter, och upphöjda noder har alltid uppdaterade data via synkron strömmande replikering i PostgreSQL.

För att kunna dra nytta av HA på koordinator-noden måste databas programmen identifiera och försöka ta bort anslutningar på nytt och misslyckade transaktioner. Den nyligen framhävda koordinatorn kommer att vara tillgänglig med samma anslutnings sträng.

Återställningen kan delas upp i tre steg: identifiering, redundans och fullständig återställning.  Storskalig körning av regelbundna hälso kontroller på varje nod, och efter fyra misslyckade kontroller avgör det att en nod är nere. Storskaligt höjer sedan ett vänte läge till den primära nodens status (redundans) och etablerar en ny vänte läge.
Streaming-replikering börjar och den nya noden aktive ras.  När alla data har repliker ATS har noden nått fullständig återställning.

### <a name="next-steps"></a>Nästa steg

- Lär dig hur du [aktiverar hög tillgänglighet](howto-hyperscale-high-availability.md) i en storskalig Server grupp.
