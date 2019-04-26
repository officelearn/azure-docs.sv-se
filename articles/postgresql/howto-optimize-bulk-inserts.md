---
title: Optimera bulkinfogningar på en Azure Database for PostgreSQL-server
description: Den här artikeln beskrivs hur du kan optimera bulk insert-åtgärder för en Azure Database for PostgreSQL-server.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 10/22/2018
ms.date: 03/04/2019
ms.openlocfilehash: a82984ce4c2a2e44306abaa63265e0c25cc6ace4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422125"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql-server"></a>Optimera bulkinfogningar och använda tillfälliga data på en Azure Database for PostgreSQL-server 
Den här artikeln beskrivs hur du kan optimera bulk insert-åtgärder och använda tillfälliga data på en Azure Database for PostgreSQL-server.

## <a name="use-unlogged-tables"></a>Använda ej loggade tabeller
Om du har arbetsbelastningsåtgärder som innebära tillfälliga data eller som Infoga stora datauppsättningar i grupp kan du använda ej loggade tabeller.

Ej loggade tabeller är en PostgreSQL-funktion som kan användas effektivt för att optimera bulkinfogningar. PostgreSQL använder Write-Ahead loggning (WAL). Det ger Atomicitet och hållbarhet, som standard. Atomicitet, konsekvens, isolering och hållbarhet utgör ACID-egenskaper. 

Lägga till i en ej loggade tabell innebär att PostgreSQL sker infogningar utan att behöva skriva i transaktion loggning, som i sin tur är en i/o-åtgärd. Därför kan är dessa tabeller betydligt snabbare än vanliga tabeller.

Använd följande alternativ för att skapa en ej loggade tabell:
- Skapa en ny ej loggade tabell med hjälp av syntaxen `CREATE UNLOGGED TABLE <tableName>`.
- Konvertera en befintlig loggas tabell till en ej loggade tabell med hjälp av syntaxen `ALTER TABLE <tableName> SET UNLOGGED`.  

Om du vill ångra processen, Använd syntax `ALTER TABLE <tableName> SET LOGGED`.

## <a name="unlogged-table-tradeoff"></a>Ej loggade tabell kompromiss
Ej loggade tabellerna inte krascher safe. En ej loggade tabell trunkeras automatiskt efter en krasch eller omfattas av en felaktig avstängning. Innehållet i en ej loggade tabellen replikerats inte till reservservrar. Skapa en tabell som ej loggade index är automatiskt ej loggade också. Efter infogningen åtgärden har slutförts, kan du konvertera tabellen till inloggad så att infogningen är beständiga.

Vissa slutkundsarbetsbelastningar ha uppstått cirka en 15 procent till 20 procent prestandaförbättring när ej loggade tabeller har använts.

## <a name="next-steps"></a>Nästa steg
Granska din arbetsbelastning för användning av tillfälliga data och stora bulkinfogningar. Se följande PostgreSQL-dokumentation:
 
- [Skapa tabell SQL-kommandon](https://www.postgresql.org/docs/current/static/sql-createtable.html)