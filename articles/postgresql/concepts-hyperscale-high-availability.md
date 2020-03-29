---
title: Hög tillgänglighet – Hyperskala (Citus) – Azure-databas för PostgreSQL
description: Begrepp för hög tillgänglighet och katastrofåterställning
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975541"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Hög tillgänglighet i Azure Database för PostgreSQL – Hyperskala (Citus)

Ha undviker stilleståndstider med hög tillgänglighet (HA) genom att underhålla reservrepliker för varje nod i en servergrupp. Om en nod går ned växlar Hyperskala inkommande anslutningar från den misslyckade noden till dess vänteläge. Redundans sker inom några minuter och marknadsförda noder har alltid nya data via PostgreSQL synkron direktuppspelningsreplikering.

För att dra nytta av HA på koordinatorn noden, databasprogram måste identifiera och försöka tappade anslutningar och misslyckade transaktioner. Den nyligen befordrade koordinatorn kommer att vara tillgänglig med samma anslutningssträng.

Återställning kan delas upp i tre steg: identifiering, redundans och fullständig återställning.  Hyperskala körs periodiska hälsokontroller på varje nod, och efter fyra misslyckade kontroller avgör den att en nod är nere. Hyperskala befordrar sedan en standby-till-primär nodstatus (redundans) och etablerar en ny standby-to-be.
Strömmande replikering börjar, vilket gör den nya noden uppdaterad.  När alla data har replikerats har noden nått fullständig återställning.

### <a name="next-steps"></a>Nästa steg

- Lär dig hur du [aktiverar hög tillgänglighet](howto-hyperscale-high-availability.md) i en hyperskalaservergrupp.
