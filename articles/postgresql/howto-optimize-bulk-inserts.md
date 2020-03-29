---
title: Optimera massinfogningar – Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du kan optimera massinfogningsåtgärder på en Azure-databas för PostgreSQL - Single Server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4c4bac16917be0064ebb111328753d378d462a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770143"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Optimera massinfogningar och använda tillfälliga data i en Azure-databas för PostgreSQL - Single Server 
I den här artikeln beskrivs hur du kan optimera massinfogningsåtgärder och använda tillfälliga data på en Azure-databas för PostgreSQL-server.

## <a name="use-unlogged-tables"></a>Använd ologgade tabeller
Om du har arbetsbelastningsåtgärder som omfattar tillfälliga data eller som infogar stora datauppsättningar i grupp kan du överväga att använda ologgade tabeller.

Unlogged tabeller är en PostgreSQL-funktion som kan användas effektivt för att optimera bulk skär. PostgreSQL använder Write-Ahead Logging (WAL). Det ger atomicitet och hållbarhet, som standard. Atomicitet, konsistens, isolering och hållbarhet utgör ACID-egenskaperna. 

Att infoga i en ologgad tabell innebär att PostgreSQL infogar utan att skriva in i transaktionsloggen, vilket i sig är en I/O-åtgärd. Som ett resultat är dessa tabeller betydligt snabbare än vanliga tabeller.

Använd följande alternativ för att skapa en ologgad tabell:
- Skapa en ny ologgad tabell `CREATE UNLOGGED TABLE <tableName>`med syntaxen .
- Konvertera en befintlig loggad tabell till en ologgad tabell med syntaxen `ALTER TABLE <tableName> SET UNLOGGED`.  

Om du vill vända `ALTER TABLE <tableName> SET LOGGED`på processen använder du syntaxen .

## <a name="unlogged-table-tradeoff"></a>Ologgad bordsavvägning
Olästa bord är inte kraschsäkra. En oläst tabell trunkeras automatiskt efter en krasch eller utsätts för en oren avstängning. Innehållet i en ologgad tabell replikeras inte heller till standby-servrar. Alla index som skapas på en ologgad tabell är automatiskt ologgade också. När insatsen är klar konverterar du tabellen till inloggad så att insatsen är hållbar.

Vissa kundarbetsbelastningar har upplevt cirka 15 till 20 procent prestandaförbättring när ologgade tabeller användes.

## <a name="next-steps"></a>Nästa steg
Granska din arbetsbelastning för användning av tillfälliga data och stora massinfogningar. Se följande PostgreSQL-dokumentation:
 
- [Skapa SQL-kommandon för tabell](https://www.postgresql.org/docs/current/static/sql-createtable.html)
