---
title: Optimera bulkinfogningar i Azure Database for PostgreSQL-server
description: Den här artikeln beskrivs hur du kan optimera bulk insert-åtgärder för Azure Database for PostgreSQL-server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 9d2bfcddc649e4fff68bdba49df0945e88067036
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545244"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>Optimera bulkinfogningar och användning av tillfälliga data på Azure Database for PostgreSQL-server 
Den här artikeln beskrivs hur du kan optimera bulk insert-åtgärder och användning av tillfälliga data på en Azure Database for PostgreSQL-server.

## <a name="using-unlogged-tables"></a>Med hjälp av ej loggade tabeller
För kunder som har arbetsbelastningsåtgärder som innebära tillfälliga data eller som Infoga stora datauppsättningar i grupp, Överväg att använda ej loggade tabeller.

Ej loggade tabeller är en PostgreSQL-funktion som kan användas effektivt för att optimera bulkinfogningar. PostgreSQL använder Write-Ahead loggning (WAL), vilket ger Atomicitet och hållbarhet två av ACID-egenskaper som standard. Lägga till i en ej loggade tabell skulle medelvärdet PostgreSQL gör infogningar utan att behöva skriva i transaktionsloggen, är som i sin tur en i/o-åtgärd, vilket gör dessa tabeller betydligt snabbare än vanliga tabeller.

Nedan visas alternativen för att skapa en ej loggade tabell:
- Skapa en ny ej loggade tabell med syntax: `CREATE UNLOGGED TABLE <tableName>`
- Konvertera en befintlig loggas tabell till en ej loggade tabell med syntax: `ALTER <tableName> SET UNLOGGED`.  Detta kan ångras genom att använda syntaxen: `ALTER <tableName> SET LOGGED`

## <a name="unlogged-table-tradeoff"></a>Ej loggade tabell kompromiss
Ej loggade tabeller är inte krascher safe. En ej loggade tabell trunkeras automatiskt efter en krasch eller omfattas av en felaktig avstängning. Innehållet i en ej loggade tabellen finns också inte har replikerats till reservservrar. Skapa en tabell som ej loggade index är automatiskt ej loggade också.  När infogningen har slutförts kan du konvertera tabellen till loggas så att infogningen är beständiga.

Men på vissa slutkundsarbetsbelastningar vi har råkat ut ungefär en 15-20 procent prestandaförbättring när du använder ej loggade tabeller.

## <a name="next-steps"></a>Nästa steg
Granska din arbetsbelastning för användning av tillfälliga data och stora bulkinfogningar.  

Granska följande dokumentation för PostgreSQL - [Skapa tabell SQL-kommandon](https://www.postgresql.org/docs/current/static/sql-createtable.html)