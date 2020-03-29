---
title: Versioner som stöds - Azure Database för PostgreSQL - Single Server
description: Beskriver de postgres-huvud- och delversioner som stöds i Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792231"
---
# <a name="supported-postgresql-major-versions"></a>PostgreSQL-huvudversioner som stöds
Microsoft strävar efter att stödja n-2-versioner av PostgreSQL-motorn i Azure Database for PostgreSQL - Single Server. Versionerna skulle vara den aktuella huvudversionen på Azure (n) och de två tidigare huvudversionerna (-2).

Azure Database för PostgreSQL stöder för närvarande följande huvudversioner:

## <a name="postgresql-version-11"></a>PostgreSQL version 11
Den nuvarande mindre utgåvan är 11,5. Se [PostgreSQL-dokumentationen](https://www.postgresql.org/docs/11/static/release-11-5.html) om du vill veta mer om förbättringar och korrigeringar i den här mindre versionen.

## <a name="postgresql-version-10"></a>PostgreSQL version 10
Den nuvarande mindre utgåvan är 10.10. Se [PostgreSQL-dokumentationen](https://www.postgresql.org/docs/10/static/release-10-10.html) om du vill veta mer om förbättringar och korrigeringar i den här mindre versionen.

## <a name="postgresql-version-96"></a>PostgreSQL version 9.6
Den nuvarande mindre utgåvan är 9.6.15. Se [PostgreSQL-dokumentationen](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) om du vill veta mer om förbättringar och korrigeringar i den här mindre versionen.

## <a name="postgresql-version-95"></a>PostgreSQL version 9.5
Den nuvarande mindre utgåvan är 9.5.19. Se [PostgreSQL-dokumentationen](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) om du vill veta mer om förbättringar och korrigeringar i den här mindre versionen.

## <a name="managing-upgrades"></a>Hantera uppgraderingar
PostgreSQL-projektet utfärdar regelbundet mindre versioner för att åtgärda rapporterade fel. Azure Database for PostgreSQL korrigerar automatiskt servrar med mindre versioner under tjänstens månatliga distributioner. 

Automatisk uppgradering av högre version stöds inte. Det finns till exempel ingen automatisk uppgradering från PostgreSQL 9.5 till PostgreSQL 9.6. Om du vill uppgradera till nästa högre version skapar du en [databasdump och återställer](./howto-migrate-using-dump-and-restore.md) till en server som skapades med den nya motorversionen.

### <a name="version-syntax"></a>Syntax för version
Innan PostgreSQL version 10, [PostgreSQL versionshantering politik](https://www.postgresql.org/support/versioning/) anses vara en _större version_ uppgradering vara en ökning av det första _eller_ andra numret. Till exempel ansågs 9,5 till 9,6 vara en _viktig_ version uppgradering. Från och med version 10 anses endast en ändring av det första numret vara en större versionsuppgradering. Till exempel är 10.0 till 10.1 en _mindre_ version uppgradering. Version 10 till 11 är en _större_ version uppgradering.

## <a name="next-steps"></a>Nästa steg
Information om postgreSQL-tillägg som stöds finns [i tilläggsdokumentet](concepts-extensions.md).
