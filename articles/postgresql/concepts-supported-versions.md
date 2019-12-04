---
title: Versioner som stöds – Azure Database for PostgreSQL-enskild server
description: Beskriver de postgres huvud-och del versioner som stöds i Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792231"
---
# <a name="supported-postgresql-major-versions"></a>PostgreSQL huvud versioner som stöds
Microsoft syftar till att stödja n-2-versioner av PostgreSQL-motorn i Azure Database for PostgreSQL-Single-Server. Versionerna är den aktuella huvud versionen av Azure (n) och de två tidigare huvud versionerna (-2).

Azure Database for PostgreSQL stöder för närvarande följande huvud versioner:

## <a name="postgresql-version-11"></a>PostgreSQL-version 11
Den aktuella del versionen är 11,5. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/11/static/release-11-5.html) .

## <a name="postgresql-version-10"></a>PostgreSQL version 10
Den aktuella del versionen är 10,10. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/10/static/release-10-10.html) .

## <a name="postgresql-version-96"></a>PostgreSQL-version 9,6
Den aktuella del versionen är 9.6.15. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) .

## <a name="postgresql-version-95"></a>PostgreSQL-version 9,5
Den aktuella del versionen är 9.5.19. Läs postgresql- [dokumentationen](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) om du vill veta mer om förbättringar och korrigeringar i den här mindre versionen.

## <a name="managing-upgrades"></a>Hantera uppgraderingar
PostgreSQL-projektet utfärdar regelbundet mindre versioner för att åtgärda rapporterade buggar. Azure Database for PostgreSQL automatiskt korrigering av servrar med mindre versioner under tjänstens månatliga distributioner. 

Automatisk uppgradering av högre version stöds inte. Det finns till exempel ingen automatisk uppgradering från PostgreSQL 9.5 till PostgreSQL 9.6. Om du vill uppgradera till nästa högre version skapar du en [databasdump och återställer](./howto-migrate-using-dump-and-restore.md) till en server som skapades med den nya motorversionen.

### <a name="version-syntax"></a>Versions syntax
Innan PostgreSQL version 10 anses den [postgresql versions principen](https://www.postgresql.org/support/versioning/) en _större versions_ uppgradering för att öka det första _eller_ andra numret. 9,5 till 9,6 ansågs till exempel vara en _huvud_ versions uppgradering. Från och med version 10 betraktas bara en ändring i det första talet som en huvud versions uppgradering. Till exempel är 10,0 till 10,1 en _mindre_ versions uppgradering. Version 10 till 11 är en _huvud_ versions uppgradering.

## <a name="next-steps"></a>Nästa steg
Information om PostgreSQL-tillägg som stöds finns i [tillägg-dokumentet](concepts-extensions.md).
