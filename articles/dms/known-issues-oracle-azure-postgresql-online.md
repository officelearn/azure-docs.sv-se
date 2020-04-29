---
title: 'Kända problem: Migrera från Oracle till Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Läs om kända problem och begränsningar för migrering med online-migrering från Oracle till Azure Database for PostgreSQL-en server med hjälp av Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235247"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Kända problem/migrerings begränsningar med online-migreringar från Oracle till Azure DB för PostgreSQL – en server

Kända problem och begränsningar som är kopplade till online-migreringar från Oracle till Azure Database for PostgreSQL – en server beskrivs i följande avsnitt.

## <a name="oracle-versions-supported-as-a-source-database"></a>Oracle-versioner som stöds som käll databas

Azure Database Migration Service stöder anslutning till:

- Oracle version 10g, 11g och 12C.
- Oracle Enterprise, standard, Express och personal Edition.

Azure Database Migration Service stöder inte anslutning till behållar databaser för flera innehavare (CDBs).

## <a name="postgresql-versions-supported-as-a-target-database"></a>PostgreSQL-versioner som stöds som mål databas

Azure Database Migration Service stöder migrering till Azure Database for PostgreSQL-en server version 9,5, 9,6, 10 och 11. Se artikeln [PostgreSQL Database-versioner som stöds](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) för aktuell information om versions stöd i Azure Database for PostgreSQL-enskild server.

## <a name="datatype-limitations"></a>Begränsningar för data typer

Följande data typer **kommer inte** att migreras:

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Kapslade tabeller
- Användardefinierade data typer
- Obs!
- Virtuella kolumner
- Materialiserade vyer baserat på kolumnerna ROWID

Tomma BLOB/CLOB-kolumner mappas också till NULL på målet.

## <a name="lob-limitations"></a>LOB-begränsningar

- När ett LOB-läge med begränsad storlek är aktiverat replikeras tom LOBs på Oracle-källan som NULL-värden.
- Långa objekt namn (över 30 byte) stöds inte.
- Data i LONG-och LONG RAW-kolumn får inte överskrida 64 kB. Data som är större än 64 KB trunkeras.
- I Oracle 12 stöds inte ändringar i LOB-kolumner (migreras).
- Uppdateringar av XMLTYPE-och LOB-kolumner stöds inte (migreras).

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

- Kunder måste använda SYSDBA för att ansluta till Oracle.
- Data ändringar som orsakas av åtgärder för partition/under partition (Lägg till, släpp, EXCHANGE och TRUNKERA) kommer inte att migreras och kan orsaka följande fel:
  - För att lägga till åtgärder kan uppdateringar och borttagningar av tillagda data returnera varningen "0 rader som påverkas".
  - För DROP-och TRUNKe-åtgärder kan nya infogningar resultera i "dubbletter"-fel.
  - För EXCHANGE-åtgärder kan både "0 rader som påverkas" varnings-och "dubbletter"-fel uppstå.
- Tabeller vars namn innehåller apostrofer kan inte replikeras.
