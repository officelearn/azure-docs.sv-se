---
title: 'Kända problem: Migrera från Oracle till Azure Database för PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Lär dig mer om kända problem och migreringsbegränsningar med onlinemigreringar från Oracle till Azure Database för PostgreSQL-Single-server med hjälp av Azure Database Migration Service.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235247"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Kända problem/migreringsbegränsningar med onlinemigreringar från Oracle till Azure DB för PostgreSQL-Single-server

Kända problem och begränsningar som är associerade med onlinemigreringar från Oracle till Azure Database för PostgreSQL-Single-server beskrivs i följande avsnitt.

## <a name="oracle-versions-supported-as-a-source-database"></a>Oracle-versioner stöds som en källdatabas

Tjänsten för migrering av Azure Database stöder anslutning till:

- Oracle version 10g, 11g och 12c.
- Oracle Enterprise, Standard, Express och Personal Edition.

Azure Database Migration Service stöder inte anslutning till cd-kort för flera innehavare.

## <a name="postgresql-versions-supported-as-a-target-database"></a>PostgreSQL-versioner stöds som måldatabas

Azure Database Migration Service stöder migreringar till Azure Database för PostgreSQL-Single server version 9.5, 9.6, 10 och 11. Se artikeln [PostgreSQL-databasversioner som stöds](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) för aktuell information om versionsstöd i Azure Database för PostgreSQL-Single-server.

## <a name="datatype-limitations"></a>Begränsningar för datatyp

Följande datatyper **kommer inte** att migreras:

- BFILE (BFILE)
- ROWID (RADID)
- Referens
- UROWID (UROWID)
- Anydata
- SDO_GEOMETRY
- Kapslade tabeller
- Användardefinierade datatyper
- Anteckningar
- Virtuella kolumner
- Materialiserade vyer baserat på ROWID-kolumn

Tomma BLOB/CLOB-kolumner mappas också till NULL för målet.

## <a name="lob-limitations"></a>LOB-begränsningar

- När LOB-läge i begränsad storlek är aktiverat replikeras tomma LOBs på Oracle-källan som NULL-värden.
- Långa objektnamn (över 30 byte) stöds inte.
- Data i kolumnen LONG och LONG RAW kan inte överstiga 64k. Alla data utöver 64k kommer att trunkeras.
- Endast i Oracle 12 stöds inte några ändringar i LOB-kolumner (migrerade).
- UPDATEs till XMLTYPE- och LOB-kolumner stöds inte (migrerade).

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

- Kunder måste använda SYSDBA för att ansluta till Oracle.
- Dataändringar till följd av partitions-/delpartitionsåtgärder (ADD, DROP, EXCHANGE och TRUNCATE) migreras inte och kan orsaka följande fel:
  - För ADD-åtgärder kan uppdateringar och borttagningar på tillagda data returnera en "0 rader som påverkas" varning.
  - För DROP- och TRUNKERINGsåtgärder kan nya skär resultera i "dubbletter"-fel.
  - För EXCHANGE-åtgärder kan både en "0 rader påverkas" varning och "dubbletter" fel uppstå.
- Tabeller vars namn innehåller apostrofer kan inte replikeras.
